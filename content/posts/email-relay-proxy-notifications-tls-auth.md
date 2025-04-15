---
date: '2025-04-14T22:43:39+02:00'
draft: true
title: 'Email relay/proxy on Linux, or in FreeBSD jail, with TLS and authentication'
tags: ["Linux", "Email", "FreeBSD", "TrueNAS"]
---

My mum has solar panels on her roof, and an inverter that sends daily production summaries and alerts by email. But its SMTP client *does* support authentication, it does not support TLS. And I'm not sending email credentials in clear text over the internet.

Luckily, there's [E-MailRelay](https://emailrelay.sourceforge.net/): "a lightweight SMTP store-and-forward mail server". It's cross-platform too.

<!--more-->

I've configured this on the same local network as the inverter, accepting unencrypted and unauthenticated SMTP connections, relaying them to my external email provider with authentication over TLS.

I originally had this running on a Netbook ([remember those?](https://en.wikipedia.org/wiki/Netbook)) running Windows 7. Then in a (FreeBSD) jail on a FreeNAS, now [TrueNAS "Core"](https://www.truenas.com/truenas-core/). But after many years of 24x7 service, the USB-stick it ran off (which is advised against!) recently stopped working.

Since that NAS wasn't really used for much else anymore, I replaced it with an old Raspberry Pi 2B, with significant less power-consumption.

## Installing

On 64-bit Linux, installing E-MailRelay is presumably as easy as...

```bash
curl -L https://master.dl.sourceforge.net/project/emailrelay/emailrelay/2.6/emailrelay_2.6_amd64-deb12.deb -o emailrelay.deb
sudo apt install ./emailrelay.deb
```

But there are no binaries for 32-bit platforms (like Raspberry Pi 2B) of Linux or FreeBSD available on the project. But we can compile  (with OpenSSL support) and create the necessary directories for our configuration like so:

```bash
sudo apt-get install libssl-dev
curl -L https://cyfuture.dl.sourceforge.net/project/emailrelay/emailrelay/2.6/emailrelay-2.6-src.tar.gz -o emailrelay.tar.gz
tar xvfz emailrelay.tar.gz
cd emailrelay-2.6
./configure --enable-std-thread --with-openssl
make
sudo make install
sudo mkdir -p /etc/emailrelay
sudo mkdir -p /var/spool/emailrelay
```

## Configuring

### Configuring E-MailRelay

First we'll store credentials for authenticating against our upstream SMTP server, replacing `upstream-username` and `upstream-password`, and ensuring the file is only readable to `root`:

```bash
echo client plain "upstream-username" "upstream-password" | sudo tee /etc/emailrelay/emailrelay.auth
sudo chmod 600 /etc/emailrelay/emailrelay.auth
```

Next, we'll configure E-mailRelay to act as a proxy for the local network only, and forward emails to the specified upstream server (here `upstream-smtp-server:587`), using the credentials we stored in `/etc/emailrelay/emailrelay.auth`:

```bash
sudo tee /etc/emailrelay/emailrelay.conf << EOF
as-proxy upstream-smtp-server:587
client-tls
client-auth /etc/emailrelay/emailrelay.auth
log
log-file /var/log/emailrelay.log
log-format time,msgid
pid-file /run/emailrelay/emailrelay.pid
spool-dir /var/spool/emailrelay
#verbose
EOF
```

See also: [E-MailRelay configuration reference](https://emailrelay.sourceforge.net/index.html#reference_md_Configuration).

### Configuring systemd service

Finally, we'll configure a systemd service to start E-MailRelay automatically, using our configuration file:

```bash
sudo tee /etc/systemd/system/emailrelay.service << 'EOF'
[Unit]
Description=E-MailRelay mail server
Documentation=man:emailrelay(1)
After=network-online.target
Wants=network-online.target

[Service]
Type=forking
Restart=on-success
KillMode=control-group
ExecStart=/usr/local/sbin/emailrelay /etc/emailrelay/emailrelay.conf
ExecStop=/bin/kill -15 $MAINPID
PIDFile=/run/emailrelay/emailrelay.pid

[Install]
WantedBy=multi-user.target
EOF
```

Reload services, start `emailrelay` and check its status:

```bash
sudo systemctl daemon-reload
sudo systemctl start emailrelay
sudo systemctl status emailrelay
```

## Verifying setup

So our service is running, but is it actually capable of forwarding emails?

While you can use `telnet` to send [raw SMTP commands](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol#SMTP_transport_example) to E-MailRelay, it's much easier to test with a tool like sSMTP.

Install it using APT: `sudo apt install ssmtp` then configure a view options in its config file `/etc/ssmtp/ssmtp.conf` (replace `from-domain.tld` with your own domain name):

```ini
root=postmaster
mailhub=localhost:25
rewriteDomain=from-domain.tld
hostname=raspberrypi
FromLineOverride=YES
```

Send a test email:

```bash
echo -e "Subject: Test Email\n\nThis is a test email." | ssmtp -v -f"from-address@from-domain.tld" to-address@to-domain.tld
```

## Monitoring

To check if your service is running and latest log output:

```bash
sudo systemctl status emailrelay
```

Inspect systemd logs for the E-MailRelay service:

```bash
journalctl -eu emailrelay # go to end

journalctl -fu emailrelay # follow
```

Follow E-MailRelay's own log only

```bash
sudo tail -f -n 50 /var/log/emailrelay.log
```

## FreeBSD jail on TrueNAS

There are few differences and prerequisites, if you want to run E-MailRelay in a FreeBSD jail instead of Linux.

To the best of my recollection, this is what I did when I ran it (for years) on TrueNAS.

### Creating jail on TrueNAS

- Create jail using "Basic" wizard
- Set static IP address
- Leave `VNET` unchecked
- Will fail to start - go back and uncheck `Berkeley Packet Filter`

### SSH access to jail

Using the built-in terminal is cumbersom, I prefer to enable SSH:

```shell
passwd
pkg install curl nano # just for convenience later
sed -i '' 's/#PermitRootLogin no/PermitRootLogin yes/g' /etc/ssh/sshd_config
sed -i '' 's/#PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
echo sshd_enable="YES" >> /etc/rc.conf
service sshd start
```

### Auto-start Emailrelay

Finally, to configure E-MailRelay to start automatically in the jail:

```shell
mkdir /usr/local/etc/rc.d/
touch /usr/local/etc/rc.d/emailrelay
chmod +x /usr/local/etc/rc.d/emailrelay
echo emailrelay_enable="YES" >> /etc/rc.conf
```
