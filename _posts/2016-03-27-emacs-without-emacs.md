---
layout: post
title: Emacs without Emacs
---

Vim is a very expressive language for editing text, and many other tools
provide Vim-style key bindings to interact with them, although the Emacs-style
key bindings are often the default.

Most Vim emulators provide very limited and inconsistent behaviour (except for,
ironically, [Emacs](https://www.emacswiki.org/emacs/Evil)), but I also find
that in a command prompt, the Emacs bindings are more pleasant to work with. In
this context, you usually only want to perform one or two actions, so switching
between modes is both tedious and confusing, especially when it does not quite
behave as full Vim would do anyway.

Here are the mappings I use most frequently in `bash` or basically any command
prompt or REPL. Try them in other programs - you might be surprised by how
often they work.

* `<C-A>` goes to the beginning of the line
* `<C-E>` goes to the end of the line
* `<C-F>` and `<C-B>` goes forward and backward by character
* `<A-F>` and `<A-B>` goes forward and backward by word
* `<C-U>` removes everything before the cursor
* `<C-K>` removes everything after the cursor

_You might need to tweak the settings of your terminal emulator for the alt key
to be sent through properly._

I find these mappings much more appropriate when using a command prompt
application, and if you ever need the full power of Vim, you can start a full
Vim session to edit the command with `<C-X><C-E>`.
