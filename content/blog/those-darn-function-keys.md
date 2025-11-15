---
title: "Those Darn Function Keys"
date: 2025-11-15
draft: false
---

I got a new keyboard recently. I used to use a Unicomp Classic keyboard,
however it slowly degraded and broke over time. I originally got the keyboard
for ~$80, however Unicomp has now hiked the price of their keyboards to almost
**$200**! Needless to say, but I don't want to spend that much on a keyboard.

So instead of getting a replacement Unicomp keyboard, I got a pretty good
modern mechanical keyboard[^1] for only **$45**! It lacks a number pad and it's
not as nice as the Unicomp keyboard, however it was still a *really* good deal.
There was only one problem with it when I got it though. The function keys
(`F1` through `F12`) didn't work on Linux.

## Getting function keys to work

So, from what I've seen, this is a somewhat common problem among more recent
keyboard designs. This problem most likely stems from the fact that a decent
amount of recent keyboard designs, especially more compact keyboards, use the
Apple HID (Human Interface Device) driver. While this driver works perfectly
on Windows and (obviously) macOS, by default it doesn't work properly on
Linux. By default, the function keys act as multimedia keys, and for non-US
keyboards the arrow keys sometimes don't work properly.

Luckily, I found a solution. By default the Apple HID driver starts in
function mode or "FN" mode 1. Function mode 2 allows us to properly use
our function keys. In order to change this on most Linux systems we'll need
to change the parameters for the Apple HID driver. Changing those settings can
be done like so:

- Create a new config file in `/etc/modprobe.d/` (Mine is called
`apple_hid.conf`).
- Add this line to your new config file:
`options hid_apple fnmode=2 iso_layout=0`
- Rebuild your initramfs.

For Alpine Linux, I just ran `doas apk fix mkinitfs` to rebuild the initramfs,
however for other systems the method to rebuild your initramfs is slightly
different. The `iso_layout=0` setting is for those of you who use non-US
keyboards, it does not affect US keyboards.

After doing that my function keys now work perfectly!

[^1]: For those who are curious, it is a Kisnt KN85.
