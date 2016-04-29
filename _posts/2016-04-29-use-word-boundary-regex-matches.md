---
layout: post
title: Use word boundary regex matches
---

How do you search for a word?

- **"word"** will also match _"swordsmanship"_
- **" word "** will miss _"word: "_
- etc.

The answer is simple: **word boundaries**.

In PCRE (which is a very widely used regex engine, you are likely using this)
you can do `\bword\b`, or in Vim's regex engine you can do `\<word\>` and it
does exactly what you want.
