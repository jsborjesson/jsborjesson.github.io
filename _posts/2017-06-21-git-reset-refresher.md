---
layout: post
title: Git reset refresher
---

`git reset` is a confusing command, it is used for several different things in
Git, and sometimes they seem unrelated.

* `git reset file.txt` unstages file.txt
* `git reset --hard HEAD~` deletes the current commit

I've often come back to [this
article][reset-demystified], which explains in detail how it works - I strongly
recommend reading it. What follows is a short summary that makes sense to me,
maybe it will help you as well.

It basically comes down to this:

[reset-demystified]: https://git-scm.com/blog/2011/07/11/reset.html

## Overview

| Command             | move branch | unstage changes | discard changes |
| :---                | :---:       | :---:           | :---:           |
| `git reset --soft`  | ✅          |                 |                 |
| `git reset --mixed` | ✅          | ✅              |                 |
| `git reset --hard`  | ✅          | ✅              | ✅              |
| `git reset --`      |             | ✅              |                 |

### Defaults

* Running `git reset <ref>` is the same as running `git reset --mixed <ref>` (`ref` could be a branch name or a commit sha)
* Running `git reset <path>` is the same as running `git reset -- <path>`

## Summary

`git reset` works in three steps, on the three "trees" relevant to working with git:

1. The ref (your current branch)
2. The index (added/staged changes)
3. The working directory (local changes to files)

Based on the mode (`--soft`/`--mixed`/`--hard`), it will go through this list,
starting with changing where the current branch points, then updating the index
to match, then discarding local changes and cleanly checking that out.

When provided a file argument, optionally separated by `--`, it skips the first
step, since it doesn't make sense to point an entire branch to changes of
specific files.

## Differences from checkout

It seems like there should be a row that only has a check mark in the last column, what if you only want to discard your local changes without touching anything else? That's done with `git checkout -- <path>`, or simply `git checkout <path>`.

But wait, `git checkout <ref>` also moves something, right?

Yes, `checkout` moves the `HEAD`, as opposed to `reset` which moves the branch that `HEAD` points to. You can think of moving `HEAD` as simply turning your head to look at a different box of stuff, and moving the ref as actually ripping off the label from that box and placing it on a different box.
