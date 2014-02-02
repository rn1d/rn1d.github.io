---
layout: post
title:  "Sublime Text - My New Plain Text Editor of Choice"
date:   2012-09-10 23:15:00
categories: tools
excerpt: I'm quite obsessive about my tools - I like to use the very best tools for the job. One of the tools I find absolutely essential, is a good plain text editor, so every time I set up a new work environment, a plain text editor is one of the first things I install.
---
I'm quite obsessive about my tools - I like to use the very best tools for the job. One of the tools I find absolutely essential, is a good plain text editor, so every time I set up a new work environment, a plain text editor is one of the first things I install. I've tried to minimize the number of apps that I need to install on a fresh system and use my IDEs for text file editing, but I find it problematic - Visual Studio and Eclipse do have good basic text editing features, but the sometimes sluggish performance can be annoying. There's nothing like a minimal, blazing fast editor. But on the other hand, I simply cannot understand people that like plain Notepad - it's cumbersome, offers practically no help on navigating your file, search is rudimentary, keyboard shortcuts are limited, and it's cumbersome to do indenting. In short, it feels like you have to fight it to get it to do what you want. 

Up until now my tool of choice has been [Notepad++](http://notepad-plus-plus.org/). And to be honest - it's a fine tool with many features, and free, but it does have it's shortcomings. It can be slow at times, especially with large files, and it really can't handle huge log files.  What I also sometimes find annoyingly lacking, is style - I always tweak the default style by changing the font, the app icon is horrid which it also uses for it's context menu action. It's the little things that get to you.

I've tried many alternatives, from classics like [Vim](http://www.vim.org/) (and it's modern incarnation [Cream](http://cream.sourceforge.net/)) and [Emacs](http://www.gnu.org/software/emacs/) (and it's modern incarnations), to usual contenders like [Notepad2](http://www.flos-freeware.ch/notepad2.html), to modern Metro [inspired editors](http://singularlabs.com/software/metrotextual/), to specialized apps like [WriteMonkey](http://writemonkey.com/), [OmmWriter](http://www.ommwriter.com/), [iA Writer](http://www.iawriter.com/), [WriteRoom](http://www.hogbaysoftware.com/products/writeroom) and others, but none of them quite feel right - they either lack some critical feature or are too cumbersome to use.

I noticed Sublime Text a while ago, but dismissed after playing around with it for a short while, dismissed it as not a serious tool, as it's written in Python. So on Mac I've been using [Chocolat](http://chocolatapp.com/), a really nice editor, and on Windows I've used Notepad++. However, I revisited it after read that [Ward Cunningham](http://c2.com/~ward/) is [using it](http://www.drdobbs.com/go-parallel/article/print?articleId=240000393). And I'm really glad I did.

[Sublime Text](http://www.sublimetext.com/). It does everything I need a text editor to do (and more) - with style. It has a beautiful default theme (and several to choose from), a beautiful icon and plenty of features.

It's cross platform - which is handy since I can use the same editor on Windows, OS X, and Linux.

Among my most often used features are:

 - Macros - When in need to do a monkey's job, Record (`Ctrl+Q`) an   
   action once, Playback (`Ctrl+Shift+Q`) multiple times. When used   
   properly this can be a real time saver.
 - Block selections - select a rectangular region, and delete a block    of text all at
   once. Or insert something for each line selected.

These two features alone let you give you a miniature, easy to use code/text generator that let's you quickly dispatch a tedious task that would otherwise take lots of time - create code (INSERT statements), test data, format a report, etc.

Sublime Text also features multiple selections, which allows you to select non-rectangular, non-contiguous spans of text and edit them all at once. This feature is not that easy to find - usually reserved for the higher priced editors. As far as I know, Notepad++ doesn't have it. Neither does Vim, nor Emacs. 

Sublime Text has well thought out keyboard shortcuts, and can be driven and discovered with the keyboard via it's Command Palette (`Shift+Cmd+P`) - basically an instant search for commands. 

The editor has that all the features you'd expect, and implemented in a really solid, well thought out manner:

 - Find & Replace with Regex support
 - Brace matching and auto-completion
 - Syntax-Highlighting
 - Auto - Indenting
 - File Mini-map
 - Command Palette
 - Project Management sidebar
 - Full Screen Mode, and Distraction Free mode
 - Console! - Yes, a Quake style console!

The functionality of the base editor is enhanced by Packages. It seems that there is an active creating useful features like syntax highlighting support for new languages (Powershell), and text processing functions (Markdown Preview, XML indenting). And since the packages are written in Python, it's easy to write one yourself. And one such super-useful package is the [Sublime Package Control](http://wbond.net/sublime_packages/package_control), that allows you to install new packages right from the Command Palette.

In fact, I like it so much, I've went and bought myself a license with my own hard earned money. I think it was well worth it I highly recommend checking it out.