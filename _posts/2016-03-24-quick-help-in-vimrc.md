---
layout: post
title: Quick help in vimrc
---

Normally, `K` will show you the man-page of the word under the cursor. It's a
nifty feature, but `man` is not always the most relevant command.

You can bind it to open the `:help` in Vim, when you are in a VimScript file,
this is very handy when you are working on your `vimrc` and need a quick
explanation of what a setting does.

Put this in `.vim/after/ftplugin/vim.vim`:

```vimscript
nnoremap <buffer> K :help <C-R><C-W><CR>
vnoremap <buffer> K "xy:help <C-R>x<CR>
```
