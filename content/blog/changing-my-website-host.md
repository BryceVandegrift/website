---
title: "Changing My Website Host"
date: 2022-06-26
draft: false
---

For the longest time I have used [Infinity Free](https://www.infinityfree.net/) as the host for my website since it has free website hosting.
However, I haven’t been able to successfully set up a SSL certificate without using Cloudflare.
Cloudflare has not had a good reputation and, in my opinion, [cannot be trusted](https://www.unixsheikh.com/articles/stay-away-from-cloudflare.html) [at all](https://www.devever.net/~hl/cloudflare).

## 1. [Sourcehut](https://sourcehut.org/)

Sourcehut provides static website hosting, git repository hosting, gemini hosting (sadly no gopher hosting) and many other things.
The upside of using Sourcehut versus a lot of other platforms for hosting my website is that they use only free and open source software for hosting.

Hosted websites are automatically outfitted with SSL certificates which reduces the hassle (and they don’t use Cloudflare).
My git repositories are also hosted at Sourcehut so it would make sense to move my website there.
Sourcehut also provides paste bins, mailing lists, wikis, and more useful tools.

One caveat is that, even though service is currently 100% free (as in free beer), once Sourcehut is out of alpha, there will probably be a price tag associated with all the services (although, right now the price doesn't look that bad.)

## 2. Get a VPS

This may be my best option in the long run as it’s the closest thing to self hosting that I can get.
I can host my website, git repositories, as well as almost anything else that I like including chat servers, forums, email, and etc.

The biggest downside of this is the price, which can vary from a couple dozen dollars a year to a few **hundred** dollars a year.

## 3. Move to some other 3rd party static site host

I’m probably more likely to self host my website before I resort to hosting it using *another* not well known 3rd party service.
My trust in most 3rd party hosts is slowly dwindling as the years go by.

## The verdict

I currently plan to move my website to Sourcehut hosting for the time being, however I plan to eventually self host everything I need and become self reliant.
I also might move my website to a more elegant static site generator as writing and managing everything from scratch is starting to make everything a bit more cumbersome.
