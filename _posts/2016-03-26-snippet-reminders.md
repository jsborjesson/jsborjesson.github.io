---
layout: post
title: Snippet reminders
---

There are some pieces of code, or a specific format of writing something that
you need frequently, but rarely enough to have to look them up, or at least
think for a few extra seconds to remember. A good way to mitigate this
interruption is to encode this knowledge in a snippet - even if they are short
and easy to type, but just hard to remember.

Here are a few examples I use, with a link to the definition underneath. I use
UltiSnips but they should be trivial to adapt to any other snippet system.

## alias argument order

```ruby
alias_method :{new}, :{old}
```

[Source](https://github.com/alcesleo/dotfiles/blob/d72a44c8eadf9c61a9e73bd08b1b29c6ee6ff387/nvim/UltiSnips/ruby.snippets#L46-L52)

Before this, I could never remember which argument came first.

## shebang

```bash
#!/usr/bin/env {bash}
```

[Source](https://github.com/alcesleo/dotfiles/blob/d72a44c8eadf9c61a9e73bd08b1b29c6ee6ff387/nvim/UltiSnips/all.snippets#L10-L12)

I could never seem to get the first `#`, `!`, `/` in the right order, and then
I had to remember which path `env` was in - no more!

## cron training wheels

This one is a bit bigger, the cron syntax is very succinct but unless you use it often there's a good chance to forget - therefore I have a snippet to insert some labels in a comment for me:

```cron
  *   *   *   *   * {command}
# |   |   |   |   |
# |   |   |   |   +----- day of week (0 - 7) (Sunday is 0 or 7)
# |   |   |   +------- month (1 - 12)
# |   |   +--------- day of month (1 - 31)
# |   +----------- hour (0 - 23)
# +------------- min (0 - 59)}
```

[Source](https://github.com/alcesleo/dotfiles/blob/d72a44c8eadf9c61a9e73bd08b1b29c6ee6ff387/nvim/UltiSnips/crontab.snippets)

The few times I use cron, this is a lifesaver - I also have the entire
help-comment as a placeholder so in case I just want it there while typing it
out, I can just do **tab-backspace** to get rid of it when I'm finished.
