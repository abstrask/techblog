---
date: '2025-04-08T00:25:01+02:00'
title: 'Persist Linux optimisations for Tailscale subnet routers and exit nodes'
tags: ["Linux", "Tailscale", "Ubuntu"]
---

When running `tailscale up` after reboots on my Ubuntu laptop acting as a subnet router, I would get this performance warning:

> Warning: UDP GRO forwarding is suboptimally configured on wl0, UDP forwarding throughput capability will increase with a configuration change.
>
> See https://tailscale.com/s/ethtool-config-udp-gro

Following the recommendations didn't seem to persist the suggested configuration changes after reboots though :thinking:

<!--more-->

## Tailscale proposed solution

Tailscale's knowledge base article recommends that machines running Linux, and acting as subnet routers or exit nodes, run the following command to improve UDP throughput:

```bash
NETDEV=$(ip -o route get 8.8.8.8 | cut -f 5 -d " ")
sudo ethtool -K $NETDEV rx-udp-gro-forwarding on rx-gro-list off
```

And in order to persist changes across reboots, run the following to create a `networkd-dispatcher` script:

```bash
printf '#!/bin/sh\n\nethtool -K %s rx-udp-gro-forwarding on rx-gro-list off \n' "$(ip -o route get 8.8.8.8 | cut -f 5 -d " ")" | sudo tee /etc/networkd-dispatcher/routable.d/50-tailscale
sudo chmod 755 /etc/networkd-dispatcher/routable.d/50-tailscale
```

## Working solution for Wi-Fi interfaces

The proposed solution didn't work for me - I still got the same warning when running `tailscale up` after rebooting. I suspect because the Wi-Fi interface is not `up` by the time the script runs.

Instead, I created a NetworkManager script at `/etc/NetworkManager/dispatcher.d/50-tailscale` containing the following:

```bash
#!/bin/bash
IFACE="$1"
STATUS="$2"
WL_INTERFACE="wl0" # replace with your own Wi-Fi interface

if [[ "$IFACE" == "$WL_INTERFACE" && "$STATUS" == "up" ]]; then
  /usr/sbin/ethtool -K $WL_INTERFACE rx-udp-gro-forwarding on rx-gro-list off
fi
```

After making the script executable with `chmod +x` and rebooting, the performance warnings were gone.