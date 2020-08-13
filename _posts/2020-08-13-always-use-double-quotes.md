---
layout: post
title: Always Use Double Quotes
---

In which language, you ask? All of them.

This is an age old war on many fronts, which never seems to end. It is a debate
which is almost impossible to avoid if you are at all interested in consistency
and readability.

I wanted a rule that would easily answer the question of which quote I should
use in **any** circumstance, language, or framework, without wasting more of my
life on this.

The strategy I came up with is this:

## Always use double quotes*

_* Never fight to switch - if a certain style guide has already won this battle, take their side and use it consistently in that project._

_* Still use single quotes where it lets you have less escaping._

This is the rule that I've found causes the least friction, the least amount of
effort, and is the most widely applicable. When considering the caveats above,
it works no matter the language and project style guide and can be applied
almost without thought or context.

## Language fit

### JavaScript

Both quotes types are exactly equivalent. Single quotes are very prevalent in style guides though, don't fight it.

### Python

Both quotes types are exactly equivalent.
[PEP8](https://www.python.org/dev/peps/pep-0008/#string-quotes) makes no
recommendation _except for docstrings_ which should always be double. Use
double quotes for everything else as well and never think about it.

### Ruby

**Both single and double quotes are strings, but they are different.**

This lets you use strings differently based on whether you need an interpolated
string or a more literal one.

Double quotes are more capable, but their extra features rarely get in the way,
stop flipping back and forth!

Using them accurately and consistently based on semantics is a nice idea, but
the human factor walks all over it, destroying any reason to trust this visual
cue in anything but the personal projects of pundits.

### PHP

Same as Ruby, the strings differ and people [fight about
performance](https://nikic.github.io/2012/01/09/Disproving-the-Single-Quotes-Performance-Myth.html).
It is a pointless micro-optimization and **you do not have enough string
literals for it to matter** even if it does have a performance impact.

### C# and Java

Single quotes are not even strings.

### Go

Go Fmt will force you to use double quotes.

## Counter Arguments

### Always using single quotes, they are easier to type

They are, but readability and consistency are more important than typing.

Besides, this strategy doesn't work in languages where the quotes aren't
equivalent - so **always** using single quotes is in practice not an option
unless you only write in these languages.

That makes this argument about using them _as often as possible_ but not nearly
always as you _sometimes_ **have** to use double quotes.

You also can't type this simple English sentence using single quotes without
escaping it. I don't know about you but I often write English sentences.

### Switch quotes based on semantics to improve readability

This is asking a lot of humans, and is sure to waste far more time on this war
than it can ever be worth, even if it may have readability or performance benefits.

### Randomly switch quotes based on your current mood, it doesn't matter

I respect you, it **is** a stupid topic that should not be given this much
thought. However, consistency is important and I hope you can be converted to
my very simple rule of thumb.

That being said, if you really don't care you are probably not reading this
anyway.

## Conclusion

> Strong opinions loosely held

Despite the controversial title, and vehemently picking a side in this war while
at the same time calling it pointless, my only aim with this post is to share my
internal rule of thumb which has personally let me put this issue to rest and
stop worrying about it.

I hope it may also give _you_ some peace (of mind).
