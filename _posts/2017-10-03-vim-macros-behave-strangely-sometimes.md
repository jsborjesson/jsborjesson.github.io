---
layout: post
title: Vim macros behave strangely sometimes
---

The vague title signifies how I've been categorising this issue in my head for a long time. There's basically a ghost messing with _some_ of the macros I try to write, making them do something completely different, and making me look stupid and eventually giving up.

## Broken macro

![Unsuccessful Vim macro usage](/public/images/escape-ruins-vim-macro.gif)

In this gif I try to create a macro to change some comment headers in my dotfiles to a different style (one that creates Vim folds).

Basically I want to change this:

```bash
########################################
# Copy / paste
########################################
```

To this:

```bash
{% raw %}
# }}}
# Copy / paste {{{
{% endraw %}
```

But somehow it just deletes the first line and then stops.

_Infuriating._

## The problem

If we inspect the recorded macro (you can paste it into the buffer with `"qp`, "paste from the q register"), we can see that this is what Vim saved:

```
{% raw %}
C# }}}^[jA {{{^[jdd
{% endraw %}
```

Those `^[` are how <kbd>Esc</kbd> is represented in the terminal. It is **not** two characters, it is a so called control sequence. In fact, this is how all combinations of control and some other key are represented.

_Pressing <kbd>Ctrl</kbd> + <kbd>K</kbd> is represented as `^K`._

_Pressing <kbd>Esc</kbd> is the same as pressing <kbd>Ctrl</kbd> + <kbd>[</kbd>._

It is frequently recommended to use <kbd>Ctrl</kbd> + <kbd>[</kbd> instead of <kbd>Esc</kbd> in Vim, since it can be more ergonomic to press. This is not a separate mapping, Vim simply cannot tell the difference.

Even further, combinations of <kbd>Alt</kbd> (or <kbd>Meta</kbd>, or <kbd>Option</kbd>) and other keys, are represented as <kbd>Esc</kbd> followed by that key.

_Pressing <kbd>Alt</kbd> + <kbd>J</kbd> is represented as `^[j`_

_Pressing <kbd>Alt</kbd> + <kbd>J</kbd> is the same as pressing <kbd>Esc</kbd> followed by <kbd>J</kbd>._

Herein lies the issue. When Vim executes the macro, it thinks that we are pressing <kbd>Alt</kbd> + <kbd>J</kbd>, and not <kbd>Esc</kbd> followed by <kbd>J</kbd>. This issue reveals itself only in the macro, since when we push the actual keys there's a delay between pressing <kbd>Esc</kbd> and <kbd>J</kbd>. This timeout can be set very low, but a macro is always too fast. (See `:help ttimeout`)

## The workaround

Using <kbd>Ctrl</kbd> + <kbd>C</kbd> will also get you out of insert mode, and using that in the macro works perfectly:

![Successful macro usage](/public/images/ctrl-c-fixes-vim-macro.gif)

I've tried playing with the timeout settings to no avail, and `<C-c>` does not exactly equal escape, so mapping escape to that might cause some other issues. I hope understanding this issue can reduce the frustration for someone else dealing with this hiccup, but currently I don't know a **reliable** way of fixing it other than being aware and carefully avoiding it.

If anyone knows a way to fix this for good, **please** [let me know](https://github.com/alcesleo/alcesleo.github.io/issues/new).
