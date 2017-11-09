---
layout: post
title: Writing in other languages in Vim
---

Vim is, like it or not, **made** to be used with a U.S. Qwerty layout. I've stopped switching back and forth between keyboard layouts in the OS, the U.S. layout is actually not too bad to write in the other languages that I need (Swedish, and sometimes German).

Thankfully, to write in all of these three languages, you only need a few more characters than the English alphabet:

| Letter | U.S. layout                                 | Vim       |
| **Åå** | <kbd>Alt</kbd> + <kbd>A</kbd>               | `<C-k>aa` |
| **Ää** | <kbd>Alt</kbd> + <kbd>U</kbd>, <kbd>A</kbd> | `<C-k>a:` |
| **Öö** | <kbd>Alt</kbd> + <kbd>U</kbd>, <kbd>O</kbd> | `<C-k>o:` |
| **Üü** | <kbd>Alt</kbd> + <kbd>U</kbd>, <kbd>U</kbd> | `<C-k>u:` |
| **Éé** | <kbd>Alt</kbd> + <kbd>E</kbd>, <kbd>E</kbd> | `<C-k>e'` |
| **ß**  | <kbd>Alt</kbd> + <kbd>S</kbd>               | `<C-k>ss` |

As you can see, they are somewhat reasonable both in and outside of Vim (see `:help digraphs` to get a list of Vim's characters), but there's no reason they have to be different. All of the combinations in the middle column are perfectly free in Insert mode - so why not use them in Vim too.

This maps all these letters so that you can type them in Vim's Insert mode just as you can everywhere else:

```vimscript
" Mend meta-mappings

" This is sometimes needed to get combinations with Alt to work.
" (probably not in NeoVim, check if it works without them first)
"
" CAUTION: ^[ is NOT two characters, it's the escape character.
"
" You have to insert it in Vim with <C-v><Esc>,
" then it should look like this but behave as a single character.
" If not, these settings will not work.

set <M-S-a>=^[A
set <M-a>=^[a
set <M-e>=^[e
set <M-s>=^[s
set <M-u>=^[u

" International letters
" Allow writing Swedish/German letters the 'normal' way on a US layout

noremap! <M-S-a> <C-k>AA
noremap! <M-a> <C-k>aa
noremap! <M-e>E <C-k>E'
noremap! <M-e>e <C-k>e'
noremap! <M-s> <C-k>ss
noremap! <M-u>A <C-k>A:
noremap! <M-u>O <C-k>O:
noremap! <M-u>U <C-k>U:
noremap! <M-u>a <C-k>a:
noremap! <M-u>o <C-k>o:
noremap! <M-u>u <C-k>u:
```

Note that I'm using the peculiarly named `noremap!` function, which maps them both in Insert and Command-line mode.
