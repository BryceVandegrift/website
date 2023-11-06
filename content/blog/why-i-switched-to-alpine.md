---
title: "Why I Switched to Alpine"
date: 2023-11-06T11:41:21-04:00
draft: false
---

{{< img src="/p/alpine.webp" >}}

I've been a [Void Linux](https://voidlinux.org/) user for about 2 years and I want to say it has been
a good run. Void Linux has been an absolutely great Linux distribution and
has served me well for quite a while. However, every Linux distribution is not
without it's shortcomings. Although Void Linux is a **great** distribution,
I still think that it had a few shortcomings that [Alpine Linux](https://alpinelinux.org/) fixes.

## What Alpine Does Better Than Void

### Packages

Although the main Void Linux repositories have more packages than most other
distributions, the selection is still a bit...lacking. As of writing this,
Void Linux has around 13,369 packages. Which is still **a lot** of packages,
however Alpine Linux more than **doubles** Void at around 28,583 packages[^1].

One thing that bugs me is that even though Void supports musl libc, not all
13,369 packages are available for musl. As you would have guessed, Alpine does
not have this problem since musl is the default.

Another thing that kind of bugs me is how long it took for packages I
submitted to Void's package repos to get accepted. Some of my requests
were **never** responded to and others were just forgotten about entirely.
When submitting packages to Alpine's packages repos my requests were almost
answered the same day and 100% of my packages (so far) have been accepted
into the testing branch.

### XBPS

Now don't get me wrong, the XBPS package manager is great, however apk
(Alpine's package manager) is superior when compared to XBPS. For example, when updating
my system after a month on Void it would take around 3-4 minutes to update
using XBPS. Whenever I update after a month on Alpine it usually takes
less than a minute!

A set of packages that show this perfectly are the `texlive` packages. The
time it takes to install an entire texlive distribution on disk is
notoriously long. Alpine's apk installs texlive *way* faster than XBPS.
Not only that, but apk manages cached packages in a much better way than
XBPS, in my opinion.

I don't think I will ever see a package manager as fast, light, and minimal
as apk.

### BusyBox

One thing that keeps Alpine small is instead of using the GNU Coreutils,
Alpine uses the BusyBox Coreutils. For a long time user of the GNU Coreutils
like me, the switch can be very jarring, however the payoff is (in my opinion)
worth it. The BusyBox Coreutils are actually easier to learn since they have
less features and options. They also have the upside of being more POSIX
compliant than the GNU Coreutils so writing scripts with BusyBox usually
results in more portability.

### musl, musl, musl 🐚

Void and Alpine both support musl libc, but a major difference is that Alpine
supports **only** musl[^2]. For those of you who are not well versed in C
libraries, musl is essentially a smaller and more tidy C library replacement
for glibc (the GNU C library). As I mentioned above, this means that all
28,583 Alpine packages are compiled for musl.

For reasons that I won't get into (since it would take too long) musl is
generally better than glibc, but sometimes isn't as compatible as glibc,
you can find out more about that [here](https://wiki.musl-libc.org/functional-differences-from-glibc.html).
I think one of the best comparisons of musl and glibc can be found
[here](https://www.etalabs.net/compare_libcs.html). musl binaries tend to
be smaller, faster, and more portable compared to glibc.

As a C programmer, working with musl is a **dream** compared to glibc.

### It's Great, Use It Already

Overall, Alpine Linux fixes almost every gripe that I had with Void Linux
and also fixes problems that I didn't even know I had. The entire Alpine
Linux distribution is easy to understand, simple, and fast.
There are many, many
more things that Alpine does better than Void, but if I listed them *all*
then it would take forever. But so far, Alpine Linux is probably one of the
**best** Linux distributions that I have ever used.

[^1]: These numbers where obtained by running `xbps-query -Rs "*" | wc -l`
and `apk search "*" | wc -l` respectively.

[^2]: Technically Alpine *does* support glibc if you wish to install it
through apk.
