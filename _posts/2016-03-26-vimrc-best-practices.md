---
layout: post
title: vimrc best practices
---

Well designed software is easy to change. The more often it changes, the more
important it is that it is well designed. VimScript is code, and at least in my
case, it certainly changes often. Even though I don't write sophisticated
plugins, the `vimrc` still calls for some level of style and consistency.

Here are some standards I've found to be helpful to keep things tidy:

## 1. Be specific with mappings

- Always use the `noremap` bindings unless you absolutely have to use the normal
  one. Using `nmap` instead of `nnoremap` has caused me many headaches.
- Use `xnoremap`. This is a bit of a sneaky one, `vnoremap`, despite its
  seemingly obvious name maps **both** visual and select mode - `xnoremap` maps
  only visual mode, which is usually what you mean.

## 2. Use single quoted strings

VimScript uses the `"` character for comments, strings and registers. Two
responsibilities is plenty.

## 3. Use the same casing for mappings as Vim itself

Vim allows you to specify chords and special keys case insensitively, I suggest
sticking to the same style Vim itself uses:

`<Esc>`, `<C-a>`, `<CR>`, `<S-Down>`

It is not always obvious, refer to `:help key-notation` when in doubt.

**Update:** I've opted to use the "correct" case for the "a" in `<C-a>`, even
though Vim does not always do this itself. Terminals cannot always distinguish
between `C-a` and `C-A` (or `C-S-a`), but in certain places (for example
sometimes in NeoVim) this distinction is actually made.

## Bonus: Lint your settings

There's a VimScript linter called [vint](https://github.com/Kuniwak/vint) that
will point out many other potential problems - very handy if you want to be
extra tidy.
