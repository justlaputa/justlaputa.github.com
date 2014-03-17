---
layout: post
title: "Restore Ubuntu grub2 after install Windows"
date: 2012-10-16 20:06
comments: true
categories: tips
---

A two weeks ago, I have installed Windows 8 on my home desktop to replace Windows 7, and of cause I can not boot into Ubuntu 12.04 after that. Today I got my Ubuntu live USB stick back from one of my friend, so I try to restore grub2 in Ubuntu. Here is the work flow:

<!--more-->

Boot into the Ubuntu liveUSB, and choose **Try Ubuntu** instead of install it.

Open terminal, mount necessary device:

    $ sudo mount /dev/sdx /mnt
    $ sudo mount --bind /dev /mnt/dev
    $ sudo mount --bind /proc /mnt/proc

`/dev/sdx` ==> replace `x` with the device where your old ubuntu lies in.

Then we can change root to the old ubuntu directory:

    $ sudo chroot /mnt

use [grub-install](http://www.gnu.org/software/grub/manual/html_node/Installing-GRUB-using-grub_002dinstall.html) to reinstall grub2:

    # grub-install --recheck /dev/sdx
    Installation finished. No error reported.

Now we need to **reboot**, and now we can see the ubuntu grub boot window, and boot into Ubuntu

Open terminal, use `update-grub2` to detect your windows installation:

    $ sudo update-grub2

This will find the windows installation and add it to grub2 boot entry.