---
date: '2025-04-06T14:27:24+02:00'
draft: true
title: 'No mouse, Wi-Fi (or much else really) after rebooting Ubuntu 24.04 LTS'
tags: ["Ubuntu", "Linux"]
---

I rebooted my functioning laptop, running Ubuntu 24.04 LTS, Thursday night. When it booted again, it had no detected mouse/trackpad, Wi-Fi, ethernet, Bluetooth or soundcard - pretty much all peripherals.

Several reboots and power cycles didn’t make any difference. Fairly limiting experience. Do not recommend.

<!--more-->

## Failed attempts at rolling back kernel

It was suggested to me by people more experienced with Linux than me, to attempt to roll-back the kernel. The methods I found involved listing installed kernel versions by running:

```bash
dpkg --get-selection | grep linux-image
```

However that yielded no results. Another suggestion was to select "Advanced options for Ubuntu" in the GRUB menu, but that wasn't available to me. I did try selecting "Fallback on failed updated", which seemed to make no difference.

Maybe these options or not available to me due to having SecureBoot/UEFI enabled?

In the `/boot/grub` directory, I found `kernel.efi` to be a symbolic link to `pc-kernel_2247.snap/kernel.efi`. To my relief, I found what I assume is a previous kernel version, `pc-kernel_2352.snap/kernel.efi`.

I updated the symlink to point to this older kernel, but ufortunately - as I was soon to find out - using the absolute path, instead of the relative path. After restart my computer was now completely unbootable.

By now, I had given up on it, and was mentally preparing to reinstall, and set SecureBoot to "install mode" in the BIOS - which I suspect my not be necessary at all?

## Salvation!

Before beginning to reinstall, I came across a fellow Ubuntu user, also using TPM-backed full-disk encryption, that [seems to have encountered the same issue](https://discourse.ubuntu.com/t/no-networking-after-some-upgrade-on-24-04-2-on-a-framework-16-late-2024-setup-with-secure-boot-the-tpm-for-fde/58387) around the same day.

The solution seems simple - just run:

```bash
sudo snap revert pc-kernel
```

So, I toggled SecureBoot install mode off again, booted a live Linux disto and fixed the symbolic link to the (newer) kernel, allowing me to boot again. I don't know if I could just have pointed the previous kernel.

However, now I had tripped TPM, requiring me to enter the 47 character long LUKS recovery key. It took a few failed attempts until I realised the key contains hyphens ("-") and that the prompt was probably using US keyboard layout.

At last, my computer was able to boot again with full hardware support!