---
title: "Why I Am Switching to Firefox"
date: 2023-01-05T11:51:58-05:00
draft: false
---

For the past year I have used [Qutebrowser](https://qutebrowser.org/) almost exclusively as my main
web browser. I even made a video on how extensible and great it is [here](https://youtu.be/N79au5Xq65s).
However there have been a few glaring issues with it over other browsers
like Firefox that I have noticed over the past year. Although Qutebrowser
is a great web browser, I think that these issues are too important for
me to ignore.

Now before I start bashing Qutebrowser on where it falls short of my
expectations, I should rightfully praise it for what it does right compared
to other web browsers. Qutebrowser does have a lot of cool and innovative
features that most web browsers wouldn't even think about implementing.

## What Qutebrowser does right

### Keybindings and UI

Unlike most browsers, Qutebrowser has Vim keybindings by default. This means
that every single action is bound to a specific key or combination of keys.
This makes using and navigating in Qutebrower leagues faster than in other
browsers like Firefox. There are extensions for Firefox that do allow you
to use Vim keybindings, however they are not as tightly integrated into the
browser as Qutebrowser. For Qutebrowser, **every single action has a keybinding**.

The user interface for Qutebrowser (or possibly lake thereof), in my opinion,
is very clean and nice to look at. Not only does it look nice, but it is also
highly customizable so you can change it to suit your personal preference.

### AdBlocking by default

By default, Qutebrowser has simple hosts adblocking enabled. Even though
there are far better adblocking solutions (uBlock Origin is the gold standard),
Qutebrowser still earns some points for having some sort of adblocking enabled
by default. Qutebrowser also has an adblocking mode simular to Brave Browser's
adblocking mode if hosts adblocking isn't enough for you.

### Python

{{< img src="/p/boomer-python.webp" link="/p/boomer-python.webp" alt="Boomer stuff" >}}

Now I know that using Python as a language for writing a big application has
some serious drawbacks, however in the case of Qutebrowser, writing it in
Python gives it some unique traits. For starters, writing Qutebrowser in
Python makes it easier to customize and extend in numerous ways. Being able
to program in my own keybindings, extra features, and more is (for a lack of a
better word) **awesome**. Although there are better language choices than
Python (for example Lua is a solid choice[^1]) for what it is, having
Qutebrowser written in Python comes with some great benefits

## What Qutebrowser does wrong

### Bloated

This point somewhat ties into the fact that Qutebrowser is written in Python,
but Qutebrowser is anything but small or fast. Qutebrowser uses just about
the same amount of resources on my computer as Firefox (which is considered a
bloated browser) and still runs slower. It also takes up a lot more space
on my system than Firefox as well. The executable for Qutebrowser isn't
actually that big, however the dependices needed to run it take up more space
than Firefox itself.

### Adblocking

Like I stated earlier, Qutebrowser has adblocking enabled by default, which is
really nice, however I don't think that its builtin adblocker does enough.
My standard for adblocking (as mentoned before) is uBlock Origin, and the
adblocking options that Qutebrowser provides are not really too impressive.
Compared to uBlock Origin, Qutebrowser's adblockers don't nearly do as much.

### Python

Didn't I just say that writing Qutebrowser in Python was a good thing?
Well...yes, but actually no. Python does provide Qutebrowser with an easy
language to write the program in as well as configure and extend the program
in, but the biggest downside of writing a browser in Python is speed. Qutebrowser,
being written in Python, is just a little less resource intensive than Firefox
on my system, however it is about half as fast. This compromise in performance is
expected for a program written in Python and for a browser, performance is
pretty important.

## Why Firefox?

Firefox is the only web browser that is free software and also mitigates
all of the problems I have discussed. Even though Firefox is still a bit
bloated, it does fix adblocking (with uBlock Origin) and it is written in a more sane programming
language. Firefox does not come with adblocking or keybindings by default like
Qutebrowser, but they can be added in with extensions like the aforementioned
[uBlock Origin](https://ublockorigin.com/) or [Tridactyl](https://tridactyl.xyz/).
It is sometimes a pain to change the default settings for Firefox since a lot
of useless and harmful things are enabled by default like telemetry, Pocket,
Firefox Accounts, and more. After all of that is said and done, Firefox is
actually a pretty good web browser.

Although Firefox is bloated, sometimes you need a bloated browser for
the bloated internet.

[^1]: There is actually a web browser written in Lua called [LuaKit](https://luakit.github.io/).
