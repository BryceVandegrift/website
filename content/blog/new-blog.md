---
title: "New Blog"
date: 2021-11-29
draft: false
---

Recently, I have been working on a new blog system that takes Markdown and converts it into a very simple HTML page as well as an RSS feed.
The blog system is written entirely in POSIX shell and is around 100 lines of code.

If successful, it should generate a blog page, an RSS feed, as well as a rolling blog index page.
The only downside is that it requires Pandoc which isn't that small of a package, but it makes making blog posts a lot easier to write as I can write in Markdown instead of HTML.

I based some of it from Luke Smith’s blog script [here](https://github.com/LukeSmithxyz/lb).
I have added a few quirks of my own and I also plan to greatly expand on it in the future.
