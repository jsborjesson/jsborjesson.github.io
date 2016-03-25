---
layout: post
title: "crontab: temp file must be edited in place"
---

If you try to add a cron job with `crontab -e` using Vim, without any
configuration, you will probably get this error message:

    crontab: temp file must be edited in place

Since Vim is one of, if not the most commonly used `$EDITOR`, it is a bit
surprising that this does not work out of the box. It has to do with the way
Vim handles backup files, turning that feature off (only for crontab files)
will fix the issue.

**TL;DR: put this in your `.vim/after/ftplugin/crontab.vim`:**

```vimscript
setlocal nobackup nowritebackup
```
