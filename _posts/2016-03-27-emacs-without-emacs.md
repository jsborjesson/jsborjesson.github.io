---
layout: post
title: Emacs without Emacs
---

Vim is a very expressive language for editing text, and many other tools
provide Vim-style key bindings to interact with them, although the Emacs-style
key bindings are often the default. (They are actually from Readline, which
uses Emacs-ish bindings, but some of them are different, notably `<C-u>` works
differently than in Emacs)

Most Vim emulators provide very limited and inconsistent behaviour (except for,
ironically, [Emacs](https://www.emacswiki.org/emacs/Evil)), but I also find
that in a command prompt, the Emacs bindings are more pleasant to work with. In
this context, you usually only want to perform one or two actions, so switching
between modes is both tedious and confusing, especially when it does not quite
behave as full Vim would do anyway.

Here are the mappings I use most frequently in `bash` or basically any command
prompt or REPL. Try them in other programs - you might be surprised by how
often they work.

* `<C-a>` goes to the beginning of the line
* `<C-e>` goes to the end of the line
* `<C-f>` and `<C-b>` goes forward and backward by character
* `<M-f>` and `<M-b>` goes forward and backward by word
* `<C-u>` removes everything before the cursor
* `<C-k>` removes everything after the cursor

_You might need to tweak the settings of your terminal emulator for the
`alt`/`meta` key to be sent through properly._

I find these mappings much more appropriate when using a command prompt
application, and if you ever need the full power of Vim, you can start a full
Vim session to edit the command with `<C-x><C-e>`.
