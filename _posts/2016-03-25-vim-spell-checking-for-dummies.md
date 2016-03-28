---
layout: post
title: Vim spell checking for dummies
---

`:set spell` will mark misspelled words in red, but this is not Microsoft Word.
Here are a few tricks I use to work effectively with the Vim spell checker:

* If you have [vim-unimpaired](https://github.com/tpope/vim-unimpaired)
  installed you can toggle spell checking on and off with `cos`

* Usually though, whether you want it enabled depends on the file type you are
  editing. You can put `setlocal spell` in the appropriate
  `vim/after/ftplugin/{filetype}.vim`, probably at least `markdown`
  and `gitcommit`.

* `]s` will take you to the next misspelled word.

* `z=` will bring up a list of suggestions for correcting the word under the cursor
