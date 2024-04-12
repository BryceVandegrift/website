---
title: "Using a Dot Matrix Printer in 2024"
date: 2024-02-14T11:39:49-04:00
draft: false
---

Modern printers suck. They're either overpriced, unreliable, ink/toner hogs,
or a combination of all 3.

Inkjet printers are cheap, have a good picture quality, and can print on
many surfaces. However, they are also **very** unreliable and usually don't
last over 3 years. Not only that but inkjet printer cartridges can cost
and arm and a leg, which is dumb considering ink (outside the cartridge)
is dirt cheap. Inkwell printers do solve the ink issue by having an ink
reservoir which means that you are not trapped into buying DRM infested
ink cartridges.

Laser printers are reliable, have a **great** picture quality, and are
usually more serviceable. However, they are often **very** large and
sometimes overly complicated. Just like with inkjet printers, laser printer
toner is insanely expensive and can cost anywhere from $60-$140 per cartridge.
It doesn't help that
[good] laser printers themselves are also very expensive, costing anywhere
from $500-$2,000.

Both laser and inkjet printers also suffer from having "internet" connectivity
put on them. I'm not talking about a simple LAN connection, I'm talking
about having your printer connected to Hewlett Packard's, Canon's, or
Epson's servers over the internet. Obviously, I'm not a big fan of having
my printer being able to relay information to anywhere outside my house
without my permission.

So, what are we left with after laser and inkjet printers. Well, there are
thermal printers which are really cheap, require no ink/toner, and can print
quickly. However, they require special paper, the prints are usually not high
quality, and since a thermal printer *burns* the image onto paper, the image
wears off the paper after a year or so. You also can't print in color.[^1]

I guess that means we are left with...

## Dot Matrix Printers

Dot matrix printers are relatively cheap, usually costing no more than
$500. They're also simple since a dot matrix printer's method of printing is
relatively straight forward (I'll explain how they work in a moment). Since
they're simple, that also means that they're also **very** reliable and easy
to service. Dot matrix ink ribbons are also cheap compared to inkjet ink
and laser printer toner. There are also color options for dot matrix printers.
The only real apparent downside of dot matrix printers is speed as it usually
takes a while to print compared to other printers.[^2]

## How They Work

If you're not familiar with how dot matrix printers work, you would be
surprised by how simple they are. A dot matrix printer, as the name implies,
prints in a matrix of dots. As the print head moves from left to right, it
presses the ink onto the paper via a series of tiny pins that create the
final image. It does this line by line until the entire picture is printed.
If you want to see one in action look [here](https://youtu.be/A_vXA058EDY).

{{< img src="/p/dotmatrix.webp" alt="How a dot matrix printer works" >}}

Sadly, as you can tell from the video, this means that dot matrix printers
are somewhat slow and sometimes loud compared to their contenders.

## My (Not Really) New Printer

About a year or two ago I acquired an Apple ImageWriter II off of eBay for
about $50. It had some wear and tear on it and was *severely* yellowed (which
I don't mind) and it was also previously used in an industrial setting. But
despite all of that, it worked great! The only problem it had was that one
of the pins on the print head did not work, which you will see in a moment.
Considering there is only **one** problem with it after 30 years of industrial
use is amazing and really shows the reliability of these things.

I planned to use this thing for a while, but I kept on putting it off
for about 1-2 years. It wasn't until the last 2 weeks that I decided to
get this thing working.

{{< img src="/p/imagewriter.webp" mouse="The last good Apple product" alt="Apple Imagewriter II" >}}

## Getting It To Work

### Printing Text

The first problem I encountered was how to even hook this thing up to my
modern computer. My desktop has a 9 pin serial card and the ImageWriter is a
serial printer, so I figured that I should start there. The printer came with a
cable to covert the 8 pin DIN socket on the printer to a 25 pin serial
connector, so I decided to buy a 25 pin to 9 pin null modem serial cable and a
25 pin serial coupler. I hooked it up as followed:

```
                        DB-25 Serial Port
                          |           |
DB-9 Serial Port-------+  |           | +-------DIN 8 Serial Port
                       |  |           | |
                       |  |           | |
         +-----------+ |  | +-------+ | | +---------+
         |           | v  v |       | v v |         |
         | Computer  +-+--+-+Coupler+-+-+-+ Printer |
         |           |      |       |     |         |
         +-----------+      +-------+     +---------+

```

Next, I had to get access to `/dev/ttyS1` which is where my serial port
connected to the printer is located. To do that I just created a simple udev
rule to change the permissions of `/dev/ttyS1` on boot.

```
KERNEL=="ttyS1", GROUP="plugdev", MODE="0660"
```

Finally, I had to change the settings of the serial port using `stty`.
The settings needed to be:

- 9600 baud rate
- 8 character bits
- 1 stop bit
- No parity

So in order to set them accordingly I ran:

``` sh
stty -F /dev/ttyS1 9600 cs8 -cstopb -parenb
```

And finally, I sent some data to test the printer.

``` sh
echo "This is a test" > /dev/ttyS1
```

And...**it worked!**

{{< img src="/p/testprint.webp" caption="Success!" alt="It works" >}}

### Printing PDFs

Printing text from a modern computer onto a 30 year old printer is pretty
cool, but I wanted to see how well it could print PDFs. I actually planned
to write a program to translate a PDF to a format that I could output to
the ImageWriter, needless to say, this would take a while. Luckily, I found
out that [Ghostscript](https://www.ghostscript.com/) actually supports output
to the Apple ImageWriter family. Four drivers where available:

- appledmp (Generic Apple dot matrix driver)
- iwlo (Apple ImageWriter)
- iwhi (Apple ImageWriter II)
- iwlq (Apple ImageWriter LQ)

I choose [this](https://upload.wikimedia.org/wikipedia/commons/a/af/Tux.png)
as my test image.

In order to create a file that I could send to the printer I needed to
first convert the PNG into a PDF file with ImageMagick, run Ghostscript,
and then output it to the printer:

``` sh
convert Tux.png Tux.pdf
gs -q -dBATCH -dNOPAUSE -sDEVICE=iwhi -sPAPERSIZE=letter -dFIXEDMEDIA -dPSFitPage -r160x144 -sOutputFile=tux Tux.pdf
cat tux > /dev/ttyS1
```

The output from the printer was...*interesting*.

{{< img src="/p/tuxtest.webp" alt="Failed print" >}}

> Side note, you can see the visible lines that go across the print. That is
> actually the broken pin on the print head, I plan on replacing the print
> head eventually.

This was good and bad. Good because graphics printing works.
Bad because I had no idea what was causing this image to corrupt.
At this point I had no idea what was wrong. I assumed the problem was that
the Ghostscript driver was faulty, however I tried all 4 Apple ImageWriter
drivers and they all had a similar result. At one point I thought the serial
connection was bad, but these errors were too consistent to be a connection
issue. I looked at the
[Apple ImageWriter II Technical Reference Manual](https://mirrors.apple2.org.za/ftp.apple.asimov.net/documentation/hardware/printers/Apple%20ImageWriter%20II%20Technical%20Reference%20Manual.pdf)
and that's when I found the problem.

---

#### A Side Note About Printers

One thing that I was ignorant about when it came to printers is that
almost every printer has a **buffer**. The buffer stores data for the current
print job (sometimes future print jobs as well), feeds it to the printer
once it needs to print that data, and then disposes of that data once it
has been printed. Most modern printers have a decent buffer size to hold
print data. Most printing software keeps track of how much data
is sent to the printer buffer. This means that we never really have to worry
about prints being messed up since print buffers are automatically managed
for us.

---

Once I read that the Apple ImageWriter II only has a print buffer size of
**2 kilobytes** I knew what the problem was.

The problem about sending data at 9600 baud to the printer was that it was
sending data at a faster rate than the printer could actually print, which was
overwriting data in the buffer which caused it to print garbage. In order
to remedy this I did something no man has ever done...*lower* the baud rate.

Once I set the baud rate on the printer and my computer's serial port to
2400 baud and printed the image, I got this:

{{< img src="/p/matrixtux.webp" alt="Printed image of Tux" >}}

**Success!**

There are a few errors on the right of Tux and a missing line, but that is
just a problem with Ghostscript that I can't fix. It usually does not show
up on most other prints.

### Printing Faster

The printer works perfectly at 2400 baud and lower.
The only problem is that this printer is now *slow*. 2400 baud is
not fast at all! There are pauses during printing where the printer is
waiting to receive serial data from the computer. So how do I increase the
data transfer rate but also limit the data transferred to 2kb? Well, that's
where a program called `split` comes in handy. `split` is a standard POSIX
utility that is available on **ALL** POSIX systems. It takes an input file
a splits it into multiple output files, but it also has a `-b` option
which allows you to limit the split files to a specific size. In this case,
we can limit the file size to 2kb and send them one at a time at 9600 baud.
All I have to do is run:

``` sh
split -b 2k tux
```

And now we have 67 different 2kb files starting with "x" that we can send one at a time.
In order to print them one at a time I wrote a script that just loops
over these files:

``` sh
#!/bin/sh

files="$(ls -1 x*)"

for x in $files; do
	cat $x > /dev/ttyS1
	sleep 5
done
```

Of course this is **not** an efficient way of printing. There are moments
where the printer is waiting for data to be received, but this is good enough.
In order to achieve close to 100% efficiency, you would need to
calculate how fast the print head and page feed move on the host, but
I'm *way* too lazy to do that.

## The Result

The result is a printer that is somewhat slow, but gets the job done. All
I need now is to get [tractor/continuous feed paper](https://en.wikipedia.org/wiki/Continuous_stationery) so I don't have to load
paper manually. For me, this printer is definitely a good enough solution
until I get a new printer (if this one even fails).[^3]

{{< video src="/print.webm" >}}

[^1]: I think there actually *are* thermal printers that can print in color
(I have no idea how), but they seem very rare.

[^2]: Dot matrix printers *do* have more downsides like the fact that pins
on the print head can break easily, but since you can replace the print heads
**and** print heads are relatively cheap this is negligible.

[^3]: I've been keeping my eye on any used Epson dot matrix printers. 👀
