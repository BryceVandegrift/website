---
title: "Microwave Transformers"
date: 2024-04-20T17:53:02-04:00
draft: false
---

A few years ago I was (and I still am) interested in electrical engineering
and learning about how electricity works. I built circuits and repaired
various different electronic devices around my house that were broken.
My favorite part however, was working with high voltage. The cheapest and
most effective gateway drug into high voltage that I encountered was actually
the humble microwave oven.

## The Guts of a Microwave Oven

Believe it or not, microwave ovens are actually deceptively simple. It really
consists of 3 main parts:

- The control panel
- The magnetron
- The high voltage transformer

The control panel is pretty self explanatory, it lets the
user input the time into the microwave oven, change settings, and etc.
In fact, a control panel isn't *really* necessary, all that the control
panel does is turn the transformer on and off, which could potentially be
done manually.
The magnetron is the part that actually generates the microwave radiation
needed to cook your food. The high voltage transformer is what provides the
necessary power for the magnetron to actually work. The transformer is what
we will be focusing on.

## The Transformer

If you're unfamiliar with transformers and how they work, I would recommend
learning a bit about them [here](http://hyperphysics.phy-astr.gsu.edu/hbase/magnetic/transf.html).
Otherwise, let's continue.

The transformer within a microwave oven is very special. Most high voltage
transformers that you can easily get a hold of are very...limited. Usually
these transformers output a high voltage (around 5,000 volts max), but have at
a pretty low current (less than 5 milliamps). While this can potentially hurt
you and leave burns, it is usually not enough to kill you. Microwave oven
transformers are **very** different.

While microwave oven transformers are pretty easy to get a hold of, they can
output a **scary** amount of power. The average microwave oven transformer can
output anywhere from 2,000 to 4,000 volts at around **500 milliamps** (or 0.5
amps). That's around **1-2 thousand watts** of power!

## Some Fun

With high voltage like this, you can do some really cool stuff.
But first, a disclaimer:

> ### Disclaimer
>
> This should go without saying, but do **NOT** work with microwave ovens and
> microwave oven transformers unless you know 100% what you are doing!
> These things can *easily* kill you.
>
> Human skin is usually 100 killoohms, this means that if you touch even a
> 2,000 volt microwave transformer, then you could have at *least* 20
> milliamps flow through your body[^1]. This is enough current to seize your
> muscles so you can't let go. 💀
>
> I am **NOT** responsible for anything stupid that you do.

This is the transformer that I pulled out of a microwave oven. Like most
transformers there's two coils, the primary and the secondary[^2].
These are illustrated below:

{{< img src="/p/transformer.webp" alt="Mircowave Transformer" >}}

By hooking up the primary side of the transformer to 120 volts AC (a.k.a.
Mains electricity) and attaching wires to the secondary side, I was able to get
some pretty cool arcs. I achieved this by bridging the gap between the
secondary side with a nail. This setup is called a
[Jacob's Ladder](https://teslauniverse.com/build/plans/jacobs-ladder).

{{< video src="/ac-arcs.webm" >}}

We can do better. We can build a self-sustaining (non-manual) Jacob's
Ladder...but how?

## More Power

In order to create a self-sustaining and self-starting Jacob's Ladder I needed
more power. But how exactly can we add more power to this circuit? One
microwave oven transformer already draws a lot of power, but how about **two**
microwave oven transformers? That's right, I got a hold of *another* microwave
oven transformer.

Careful consideration is needed in order to hook up two of these microwave oven
transformers. I wired the two transformers in series with a "center tap" going
straight to ground (otherwise my breaker would trip). This gives me anywhere
from 4,000 volts to 8,000 volts AC. Next I need to turn that AC voltage into
DC voltage.

Sadly, I did not have enough high voltage components to make a *true* AC to
DC conversion system...but I hobbled one together anyways. I was able to
use two microwave oven capacitors as resistors[^3] since I didn't have any
high power resistors on hand. In order to turn AC into "DC" I took and very
simple [but stupid] approach. During the negative AC cycle, all the current
is dumped into a pair of diodes...that's it. Does it waste a **lot** of power?
Yes. But does it work? **Yes**. I made a schematic below in order to better
illustrate the circuit.

{{< img src="/p/circuit.webp" link="/p/circuit.webp" alt="Jacob's Ladder circuit" >}}

And here is the final product. A **working** Jacob's Ladder!

{{< video src="/dc-arcs.webm" >}}

I'm not sure if you noticed in that video, but my breaker popped. This circuit
just draws too much power. I could improve this circuit, but I think I've
decided to end my project here.
That being said, I think this was enough high
voltage fun for one day. :)

[^1]: These are actually very conservative estimates. In reality, much more
current is likely to flow through your body...which would kill you even faster.

[^2]: Most microwave oven transformers actually have a third coil (see the
image).
This coil usually generates a very low voltage with a high current capability.
This is used to heat the magnetron filament, however we are not concerned with
it.

[^3]: For those who don't know, capacitors behave similar to resistors when
passing AC current through them due to
[capacitive reactance](http://hyperphysics.phy-astr.gsu.edu/hbase/electric/accap.html#c2).
