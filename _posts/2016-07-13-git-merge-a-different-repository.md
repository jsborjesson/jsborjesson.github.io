---
layout: post
title: Git merge a different repository
---

Ever wanted to merge a completely different repository? It's actually not very
hard, but you should consider whether it actually makes sense to do so.

```bash
# We want to merge repo1 into repo2
$ cd repo2

# Add the first repository as a remote and fetch its history
$ git remote add repo1 ../repo1
$ git fetch repo1

# Now merge it
$ git merge repo1/master
fatal: refusing to merge unrelated histories

# If the repos are entirely unrelated git will refuse
# Consider again whether this makes sense - you can force the merge with this option:
$ git merge repo1/master --allow-unrelated-histories
```

If the repos are not in neat non-conflicting folders you'll likely have some
merge conflicts that you need to solve on the way.

```bash
# If you want to clean up a bit you can now remove the remote
$ git remote remove repo1
```

And you're done!
