---
layout: post
title:  "How to fix failed arch kernel update"
published: true
author: nemanjan00
show_sidebar: true
---

If you are anything like me, it happens to you also that you battery just runs out and laptop turns off. 

The problem comes if you were just doing kernel update... 

That is the problem I had last night... 

So, how did I fix it? 

## Getting shell on that computer. 

I grabbed first linux installation I found laying around. 

It was Ubuntu Server... 

It does not have live system, but, when you start installation, you can just switch to different tty (Ctrl+Alt+F2).. 

## Getting shell inside that system. 

Now, what you need to do is chroot into your installation of arch. 

### Finding your partitions

All od your partitions should be enumerated in ``/dev/sd*`` or in ``/dev/mapper/*`` (for lvm). 

If you use have encrypted lvm/partition, you need to first run 

``` bash
cryptsetup luksOpen /dev/sdX2 name
```

And, if I remmember well, that partition/lvm will appear in: 

``` bash
/dev/mapper/name
```

### Mounting partitions

Now that you found/decrypted/etc. ypur partition, we need to mount them. 

We are going to take /mnt as base and mount your system inside. 

For example: 

```bash
mount /dev/sda2 /mnt
mount /dev/sda1 /mnt/boot
mount /dev/sdb1 /mnt/home
```

### Chrooting

If you are using arch live, you probbably have ``arch-chroot`` command. 

What you should do is just: 

``` bash
arch-chroot /mnt
```

If you are not using arch live, you first need do mount ``/sys``, ``/dev`` and ``/proc``


``` bash
cd /mnt

mount --rbind /sys sys/
mount --rbind /dev dev/
mount --rbind /proc proc/

chroot .
```

## Ok, I have shell, what now? 

Well, now you just need to install any version of kernel form cache... 

``` bash
pacman -U /var/cache/pacman/pkg/linux- # press tab and pick any version
```

Now, just reboot computer and update system. That should be it. 

