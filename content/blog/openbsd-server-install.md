---
title: "OpenBSD Server: Initial Setup"
date: 2025-02-13
draft: false
---

*This is the first part in a series of posts. Find [part 2](/blog/openbsd-httpd)
and [part 3](/blog/openbsd-smtpd) when they are ready*

---

For those of you who don't follow my blog, in my
[previous blog post](/blog/migrating-my-vps) I talked about migrating my VPS to
use OpenBSD instead of Debian stable. I recently finished migrating my VPS and
would like to document the process in order to help anyone setting up their own
OpenBSD server or VPS.

Before we get started, make sure that you have your DNS records for your domain
name pointing to your server IP address.

{{< img mouse="OpenBASED" src="/p/openbsd.webp" alt="OpenBSD logo" >}}

## Installing OpenBSD

One thing that might stop most people from using OpenBSD on a VPS specifically
is the fact that most hosting providers **don't** allow you to custom
operating systems besides the ones that they offer. Luckily, my VPS provider
[Frantech](https://my.frantech.ca/aff.php?aff=6418)[^1], allows me to upload my
own ISO images in order for you to install **any** OS that I like. So make sure
that your VPS provider actually allows you to run OpenBSD.

Go ahead and install OpenBSD normally. Here are a few tips when installing
OpenBSD for a server:

- Make sure to start `sshd` by default (otherwise you can't login)
- Create a user for your system
- Make sure you allow root ssh login (we'll need it temporarily)
- You can delete the `swap`, `/usr/X11R6`, `/usr/src/`, and `/usr/obj`
partitions when partitioning your disk as we don't need them (make sure to use
that free space though)
- Make sure you select to install **all** sets unless you know what you're
doing

**Congrats!** You now have a basic OpenBSD installation.

## Configuring

First thing is first, we need root privileges. So run this in order to connect
as the root user:

``` sh
ssh root@example.com
```

where `example.com` is your domain name. As the root user we need to create the
`/etc/doas.conf` file and add the wheel group to it. We can do so by running:

``` sh
echo "permit persist :wheel" > /etc/doas.conf
```

We can now exit our current ssh session in order to login as the user we created
when we installed OpenBSD.

Let's first update our system by running these commands using `doas`:

``` sh
doas syspatch
doas pkg_add -Uu
```

The `syspatch`[^2] command applies any necessary patches to your system and `pkg_add
-Uu` just updates packages. Speaking of adding packages, you can use `pkg_add`
in order to install your favorite text editor (you'll need it).
While we are at it, let's go ahead and disable ssh
logins for root since we don't need it anymore. Edit `/etc/ssh/sshd_config` and
add this line to it:

```
PermitRootLogin no
```

Finally, let's create the file `/etc/sysctl.conf` that will contain system-wide
settings. We will put this in `/etc/sysctl.conf`:

```
kern.maxproc=8192
kern.maxfiles=32768
kern.maxthread=16384
kern.shminfo.shmall=536870912
kern.shminfo.shmmax=2147483647
kern.shminfo.shmmni=4096
```

Please note that these settings are for **my** system and will chage from system
to system. As a rule of thumb:

- `kern.shminfo.shmmax` should be set to **half** of your maximum RAM (in bytes)
- `kern.shminfo.shmall` should be set to `kern.shminfo.shmmax` divided by 4096
(4096 is the page size in OpenBSD).

These are just a few settings that will get a bit more performance out of our
OpenBSD system. It just increases the maximum number of processes able to run at
once as well as the maximum amount of shared memory.
You can find a detailed explanation of these settings by looking
at the man page for `sysctl` (hint: run `man 2 sysctl`).

## Stay tuned

That's just the beginning of setting up an OpenBSD server, but keep a close eye
on my blog as I will upload the next part of this guide somewhat soon which will
be about setting up a static web server using `httpd`...assuming that I get
around to writing it eventually.

---

You can now read part 2 of this series [here](/blog/openbsd-httpd).

[^1]: This is an affiliate link
[^2]: Make sure to reboot whenever `syspatch` applies patches in order to reload
the OpenBSD kernel.
