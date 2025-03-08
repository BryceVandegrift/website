---
title: "FFmpeg Video Calls"
date: 2023-09-18
draft: false
---

If you have seen any of the [videos I made on](https://youtu.be/VGRHzB8ANAo)
[FFmpeg](https://youtu.be/4NKmEjzfJ98) you would know that I absolutely love
it and I think that it is the absolute **BEST** video/multimedia program ever
made.[^1] FFmpeg has so many features and it implements almost all of them
extremely well. So it comes to no surprise, after I initially made my FFmpeg
videos I found out that FFmpeg can actually be used to create video streams and
send them over the internet. After I found out about this I wondered if it
could be used as a sort of minimal way to create video calls. After
experimenting for just a few *minutes* I found a way to stream video over the
internet via FFmpeg.

``` sh
ffmpeg -f alsa -i default \ # Get audio from mic
-i /dev/video0 \ # Get video from camera
-s 640x480 \ # Scale down to 640x480
-c:v libx264 -preset:v ultrafast \ # Encode fast!
-tune zerolatency -intra-refresh 1 \ # Reduce latency
-f mpegts \ # Put everything into mpegts stream
-b:v 1M udp://localhost:1313 # Send video over localhost:1313
```

In order to actually view this video you would need a video player that is
able to read/receive from UDP. Luckily such a program exists:
[mpv](https://youtu.be/iR76e9XUodI).

``` sh
mpv --profile=low-latency udp://localhost:1313
```

Now to actually send this over the internet and not just over LAN or localhost you
would need to replace `localhost:1313` with the IP address of the recipient
for FFmpeg and the IP address of the sender for mpv. This is will let
you be able to do peer-to-peer video calls using only FFmpeg and mpv (or any
other UDP compatible video player). FFplay, a media player that comes with
FFmpeg, can play UDP streams, so technically this can be done using **only**
FFmpeg. If you replace `-f /dev/video0` with
`-i x11grab` you can also screencast to another computer as well, pretty neat.

Theoretically, you could do more than just one-on-one video calls, you could
have 4, 7, or even 15 people using this method. It would definitely be tedious
to set it up, but it would be possible. And of course if you don't want to use
video at all you can easily use this method for audio calls.

I think this example shows just how versatile FFmpeg can be. Not only that
but the settings I used for this example are not actually optimal, it was
just a test but it worked great. This is just the tip of the iceberg too,
you can also use this to stream movies from another computer,
video games, general live streaming, and much more.
I personally
find it crazy that you can basically do audio and video calls using **only**
FFmpeg and this shows just how absolutely amazing it is.

{{< video src="/cameracast.webm" >}}

[^1]: In fact, the creator of FFmpeg, Fabrice Bellard, also made QEMU and the
Tiny C Compiler (TCC) both of which are very widely used pieces of software
that are very well made.
