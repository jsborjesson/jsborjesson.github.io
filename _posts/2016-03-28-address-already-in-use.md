---
layout: post
title: Address already in use
---

Sometimes, you find yourself with a local web server running, but having lost
the console session where you started it, so you can't _ctrl-c_ to stop it.
Maybe you closed the tab, or the terminal crashed leaving the server running.
When you try to start the server again you an error like **Address already in
use**, or **A server is already running**.

For a long time I didn't really have a solution for this, it didn't happen very
frequently so I just started the server on a different port and moved on with
my life.

Some time ago I finally did the research and came up with a better solution. It
is a very simple bash script that simply kills whichever process that listens
to the port you give it, I call it `portkill` - Here it is in action:

![portkill](/public/images/portkill.gif)

It gets the id of the process listening on the port using `lsof -t -i:4000`,
then simply `kill`s that process. Here is the [source of
portkill](https://github.com/alcesleo/dotfiles/blob/master/bin/portkill), with
a tiny bit of error handling.
