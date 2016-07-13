---
layout: post
title: Emacs as evil insert mode
---

```lisp
(defalias 'evil-insert-state 'evil-emacs-state)
(define-key evil-emacs-state-map (kbd "ESC") 'evil-normal-state)
```

### Downsides:

- The `.` command will stop working for inserted text, so you cannot repeat insertions
- Autocomplete with <kbd>C-n</kbd> will not work anymore
- You can't use <kbd>Esc</kbd> as Meta anymore (but who wants that anyway?)
