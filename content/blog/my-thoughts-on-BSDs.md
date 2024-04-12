---
title: "My Thoughts on BSDs"
date: 2021-12-31T20:39:13-05:00
draft: false
---

For those of you who are not a big fan of Linux but want something that caters more to the Unix philosophy, a distribution of BSD (Berkeley Software Distribution) may be right for you.
Unlike Linux, most BSD systems’ tools, kernels, and programs are built from the ground up for that specific BSD distribution.
This also means that it can take a while for a feature or program that’s popular on Linux to make its way to a specific BSD distribution.
But, BSD it is worth a try nonetheless.

There are four major versions of BSD that I believe to be useable as an everyday operating system: FreeBSD, OpenBSD, NetBSD, and DragonFlyBSD.

## FreeBSD

[FreeBSD](https://www.freebsd.org/) is probably the most popular BSD distribution of all time.
FreeBSD has the most packages, the most support, and the most users compared to any other BSD distribution.
FreeBSD’s default file system choices are UFS (Unix File System) and ZFS (Z File System) which isn’t that bad considering ZFS is a pretty reliable file system.
FreeBSD also has support of other file systems like FAT and EXT as well as Linux binary compatibility which is pretty nice.
To be honest FreeBSD has a lot of cool and interesting features that are worth checking out.

## OpenBSD

[OpenBSD](https://www.openbsd.org/) is another flavor of BSD that is focused more on security than anything other operating system.
The people behind OpenBSD are also some of the same people behind projects like OpenSSH, LibreSSL, and even tmux (to some degree).
Although OpenBSD lacks a decent amount of software and certain features, it makes up for it in system security and stability.
Overall, I would say that it is a pretty solid operating system.

## NetBSD

[NetBSD](https://netbsd.org/) is a distribution of BSD that is focused on portability more than any other operating system out there.
NetBSD is also well known for its low resource usage as well.
NetBSD not only has support for amd64, i386, and arm, but it also supports more obscure architectures like mips, powerpc, riscv, and more.
NetBSD is also very small and can be stripped down even from it’s very small state.
I remember hearing rumors about NetBSD being able to run on a literal toaster in the past, which I find pretty impressive.
If you prefer size and portability over anything else, I would recommend NetBSD.

## DragonFlyBSD

[DragonFlyBSD](https://www.dragonflybsd.org/) is a distribution of BSD that is tailored for performance and speed.
I believe that DragonFlyBSD definitely delivers on it’s promise of speed.
Overall multi-threading and multi-processor support as well as speed is very competitive with even the fastest of Linux systems.
Swapcache, a different type of swap made for DragonFlyBSD, also helps with greatly boosting performance for large workloads.
The HAMMER file systems are also very neat.
The HAMMER file systems have instant crash recovery, snapshots, support for multiple volumes, and much more.
If you want a fast and efficient operating system, I would definitely recommend trying DragonFlyBSD.

{{< img mouse="bsd" src="/p/bsd.webp" alt="BSD" >}}
