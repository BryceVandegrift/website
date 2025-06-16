---
title: "Software"
date: 2022-09-03
draft: false
---

## OS

{{< img src="/p/alpine.webp" link="https://www.alpinelinux.org/" alt="Alpine Linux logo" >}}

For my operating system I use [Alpine Linux](https://www.alpinelinux.org/).
When it comes to operating systems, you really can't go wrong with Linux.
I use Alpine Linux because it is highly customizable and uses very little system resources.
The init system of Alpine Linux, openrc, is also very simple to understand, easy
to use, and is extremely fast. The package manager of Alpine, apk, is probably
the easiest to use and fastest package manager that I have ever seen.

## Programs

### Web Browser

My web browser of choice is [Firefox](https://www.mozilla.org/en-US/firefox/new/).
I make sure to configure Firefox to disable all telemetry, minimize the browser
footprint, reduce tracking, and more. This is usually changed in the
`about:config` page for Firefox. Although I used to use [Qutebrowser](https://qutebrowser.org/)
as my main web browser, it's downsides proved too great for me to use (you can
read more [here](https://brycevandegrift.xyz/blog/why-i-am-switching-to-firefox/)).
I also have a few extensions that I make sure to use for Firefox to make it
even better but also to block trackers and advertisements. These include:

- [uBlockOrigin](https://addons.mozilla.org/en-US/firefox/addon/ublock-origin/) (blocks ads and trackers)
- [Decentraleyes](https://addons.mozilla.org/en-US/firefox/addon/decentraleyes/) (for CDN tracking)
- [ClearURLs](https://addons.mozilla.org/en-US/android/addon/clearurls/) (for URL tracking)
- [LibRedirect](https://libredirect.github.io/) (for redirecting to privacy respecting websites)
- [Tridactyl](https://tridactyl.xyz/) (for Vim keybindings)

### Desktop Environment/Window Manager

My window manager of choice is [dwm](https://dwm.suckless.org/) (dynamic window manager).
I was pretty hesitant when I first switched over to a window manager because it seemed pretty tough to use.
However, I eventually got the hang of it and now I think that a window manager is way better for productivity.
I use dwm in particular because it takes up very little system resources and it is very customizable.
Although it takes some knowledge and skill to learn and customize dwm, it is well worth the trouble.

### Terminal

I use [st](https://st.suckless.org/) (suckless/simple terminal) for my terminal.
st is very small and uses barely any system resources, it is also pretty customizable and extensible.
Although it lacks many features that other terminal emulators have, these
features can be patched into the software by the user without much hassle.
It goes along pretty well with dwm.

### Shell

For my shell, I just use [Bash](https://www.gnu.org/software/bash/) which is
usually the default shell for most Linux distributions. It's a good shell for
interacting with the command line while not being *too* complicated like zsh
or some other shells. It has completions and is pretty extensible. I've
experimented with other shells in the past, but Bash seems to be tried and
true for me.

### Text/Code Editor

For text and code editing I use [Neovim](https://neovim.io/).
Neovim is a community driven fork of the popular Vim editor with a cleaner code base and other features like first class Lua support, sensible defaults, and is fully compatible with Vim.
I really like being able to use Lua to write scripts for Neovim rather than Vimscript, as I think that programming in Vimscript can be an absolute nightmare sometimes.

### File Manager

Normally for browsing files I just use the terminal.
I have been playing around with [lf](https://github.com/gokcehan/lf),
and I have been slowly growing more fond of it. Being able to extend it to do
things like bulk renaming, previews, and adding icons is quite satisfying and
helps me add *exactly* what I need in a file manager.

### Documents, Spreadsheets, Etc.

For creating documents I have a few systems that I like to use.
For short notes and quick write ups with plain text files or markdown files
I use [Pandoc](https://pandoc.org/) to convert my quick notes to documents or to any other formats.

For somewhat longer and more precise (but still somewhat short) documents I use
[groff](https://www.gnu.org/software/groff/)/troff for creating documents in the terminal.

For professional work and long papers/essays I use [LaTeX/XeLaTeX](https://www.latex-project.org/) for creating
documents as it makes the process for creating title pages, formatting,
citations, bibliographies, and etc. easier than using groff/troff. It definitely
beats using [Microshaft](http://catb.org/jargon/html/M/Microsloth-Windows.html) Word.

For spreadsheets I use [sc-im](https://github.com/andmarti1424/sc-im).
It's a simple terminal spreadsheet editor that has Vim keybindings by
default. It has lots of builtin features like wide character support, key
mappings, GNUPlot support, and the list goes on and on. It truly is the
Vim of spreadsheet programs.

### Video and Audio

For video I use [mpv](https://mpv.io/), almost everyone is using it nowadays and it's very versatile.
There isn't much more I can say about mpv other than it's my favorite video player.

For playing music I also use mpv. In fact I have a *very*
[simple script](https://git.sr.ht/~bpv/dotfiles/tree/master/item/.local/bin/music)
for playing the music with thumbnails from my music folder.

For editing video and audio I use [FFmpeg](https://ffmpeg.org/) as my main video editor.
FFmpeg is perhaps the gold standard for all video/audio editors, not just terminal
editors. It can manipulate video and audio streams in almost any format and
it's amazingly fast.

### Pictures and Documents

For image viewing I use [nsxiv](https://codeberg.org/nsxiv/nsxiv) because it's very simple
and follows closely with the Suckless philosophy.

For document viewing I use [zathura](https://pwmt.org/projects/zathura/) with different add-ons for PDF and document viewing.
These include `zathura-djvu` for DjVu
support, `zathura-pdf-mupdf` for PDF support, and `zathura-ps` for Postscript
support.

And finally, for presentations, I use [sent](https://tools.suckless.org/sent/) for its simplicity
as it can effortlessly turn plain text files into simple presentations.
For more professional and complex presentations I use the LaTeX
beamer class.

### Mail

I use [aerc](https://aerc-mail.org/) for email.
aerc is a terminal email client written in Go that has a lot of features that
I find particularly useful like Vim keybindings, HTML email rendering, a tmux
like embedded terminal system, and more. It's very easy to setup and works
well with large services like ProtonMail and Google (not that I would
recommend using these email services).

### Image Editing

For making small or simple changes to images I use [ImageMagick](https://imagemagick.org/index.php).
For creating images or making larger edits I use [GIMP](https://www.gimp.org/).
