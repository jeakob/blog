---
title: "HP Microserver Gen 8 - FreeBSD Bootloader"
date: 2025-12-19T21:08:00+00:00
tags: ["hp", "microserver", "freebsd"]
draft: false
---

## The Problem

The HP MicroServer Gen8 has limited and inflexible boot options. In many configurations, it cannot reliably boot an operating system directly from certain internal drives (most commonly SSDs installed in the optical bay or via internal ports). As a result, the system may fail to start even though the OS is installed correctly and the hardware is otherwise functional.

## The Solution

This guide explains how to install FreeBSD on external boot media that the HP MicroServer Gen8 can load at startup. Once loaded, the FreeBSD bootloader is used to select a configured internal drive—typically an SSD, which then boots the operating system of your choice.

{{< tip >}}
It's best to unplug all hard drives first
{{< /tip >}}

{{< note >}}
I recommend to install FreeBSD on the internal USB or micro SD card
{{< /note >}}

<figure class="image"><img style="aspect-ratio:807/499;" src="/blog/1_FreeBSD_image.png" width="807" height="499"></figure><figure class="image"><img style="aspect-ratio:802/653;" src="/blog/2_FreeBSD_image.png" width="802" height="653"></figure>

<figure class="image"><img style="aspect-ratio:354/245;" src="/blog/3_FreeBSD_image.png" width="354" height="245"></figure>

After installing FreeBSD but before restarting, drop to a shell when offered to. Assuming your new boot key is `da1`, clear whatever partitions you have first, then format it:

```plain
# gpart destroy -F da1
# gpart create -s GPT da1

```

### Install the bootloader:

```plain
# gpart bootcode -b /boot/boot0 da1

```

Assuming all four LFF drive bays are populated, configure the bootloader to use the fifth drive (the SSD) and set it as the default boot option:

```plain
# boot0cfg -Bv -o setdrv -d 0x84 -s 5 da1

```

`0x84` refers to the fifth BIOS drive. Use these if you have others:

*   `0x80` – for just the SSD (though I suppose that’s redundant)
*   `0x81` – one other drive installed
*   `0x82` – two other drives installed
*   `0x83` – three other drives isntalled
*   `0x84` – all four other drives installed

The flags are:

```plain
-B         install FreeBSD's boot0 manager
-v         like my blog, be verbose
-o setdrv  refer to the disk by BIOS drive number
-d         BIOS drive number
-s 5       boot from second disk; in this case the SSD 

```

There are several ways to approach this, but this method has been reliable for me for years.

The main drawback is that if you add additional internal drives later, you’ll need to update the boot loader with the new address. If you forget, you can always boot from the FreeBSD installer and drop to a shell to update it again. In practice, though, most people already have all the drive bays populated, which is largely the point of these small systems.

If your MicroServer still refuses to boot—as one of mine did—try connecting the bootable USB stick to one of the external USB 2.0 ports instead. One of my units may have a flaky internal port, but this workaround worked fine on another system here, as well as on the unit I keep at my dad’s place, which I’ve just realised I forgot to include in my homelab post.

### Boot Screenshot:

<figure class="image"><img style="aspect-ratio:1047/661;" src="/blog/4_FreeBSD_image.png" width="1047" height="661"></figure>

#### After a couple of seconds, you will see GRUB:

<figure class="image"><img style="aspect-ratio:1046/650;" src="/blog/FreeBSD_image.png" width="1046" height="650"></figure>