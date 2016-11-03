---
layout: post
title: Exemplary design
---

I recently wrote a simple command line tool, whose function is unimpressive and
uninteresting, but has some interesting design choices I would like to get down
in writing.

The tool is called [in that case](https://github.com/alcesleo/in_that_case) and
simply converts between different capitalization standards:

```bash
$ itc --snake inThatCase
in_that_case
```

It detects which convention is being passed in, converts it to the convention
you requested, and can even be part of pipelines:

```bash
$ echo inThatCase | itc --dash -
in-that-case
```

This post will discuss the architecture of this small, but in my opinion,
exemplary code base. I will explain why I think it is exemplary in a second.

## Discovering the project

Imagine you come across this tool, it does what you want but is lacking one of
the capitalization styles that you need. You might think that "there's probably
just one file with a huge if-statement, maybe I can try to extend it...".

So you start poking around the project. You might think that there seems to be
a lot of files for such a simple problem, and think that it looks too
complicated, but nonetheless you quickly stumble upon the `conventions/` folder
which seems to have a file for each convention it supports.


## Adding a new style

You want it to support dot-case, which looks like this: `in.that.case`.

You don't feel like getting into the project that much, you just want this damn
thing to support one more style, and at least this `conventions/` folder seems
easy enough to understand, so you decide to give it 10 minutes and break out
your most sophisticated programming tool and **copy paste** one of the
convention files. While you're at it you decide to copy paste one of the
spec-files too. Can't hurt.

Now you have essentially added this:

### Copy pasted files:

```ruby
# lib/in_that_case/conventions/snake_case.rb
require "in_that_case/convention"

module InThatCase
  module Conventions
    module SnakeCase
      extend Convention

      module_function

      def convert(words)
        words.join("_")
      end

      def extract_words(str)
        str.split("_")
      end

      def matches?(str)
        !!(str =~ /\A[a-z]+(_[a-z0-9]+)+\z/)
      end
    end
  end
end

# spec/in_that_case/conventions/snake_case_spec.rb
require "spec_helper"
require "in_that_case/conventions/snake_case"

RSpec.describe InThatCase::Conventions::SnakeCase do
  include_examples "convention"

  it ".extract_words" do
    expect(described_class.extract_words("in_that_case")).to eq %w[in that case]
  end

  it ".convert" do
    expect(described_class.convert(%w[in that case])).to eq "in_that_case"
  end
end
```

## Change everything blindly

All the files in the `conventions/` folder look pretty similar, so you decide
to just go through what you copied and change the obvious things.

- you rename the files and every occurrence of "snake" inside them to "dot"
- you change the 2 specs you copied to have dots instead of underscores
- you go through the implementation file and replace the underscores with dots
  too, there's a kind of nasty looking regex, but it has an underscore in it so
  let's take that one as well (a dot in regex has to be escaped `\.`).

Phew, that was really boring. But now you have some very similar looking
files, with different names and very slightly changed implementations:

### Altered files:

```ruby
# lib/in_that_case/conventions/dot_case.rb
require "in_that_case/convention"

module InThatCase
  module Conventions
    module DotCase
      extend Convention

      module_function

      def convert(words)
        words.join(".")
      end

      def extract_words(str)
        str.split(".")
      end

      def matches?(str)
        !!(str =~ /\A[a-z]+(\.[a-z0-9]+)+\z/)
      end
    end
  end
end

# spec/in_that_case/conventions/dot_case_spec.rb
require "spec_helper"
require "in_that_case/conventions/dot_case"

RSpec.describe InThatCase::Conventions::DotCase do
  include_examples "convention"

  it ".extract_words" do
    expect(described_class.extract_words("in.that.case")).to eq %w[in that case]
  end

  it ".convert" do
    expect(described_class.convert(%w[in that case])).to eq "in.that.case"
  end
end
```

You try running the specs to make sure you at least didn't break anything:

```bash
$ bundle exec rspec
```

You see that all of your new specs are passing, and all the old ones still pass
as well. Great! The boring part is over, let's see what's left to do to hook
this in. You try running the binary just to see what breaks:

```bash
$ exe/itc --dot myTestingStuff
my.testing.stuff
```

What? It's already works? You look at the help message to make sure that you
don't forget to add documentation:

```bash
$ exe/itc --help
In That Case

Usage:
  exe/itc (--camel | --dash | --dot | --pascal | --snake) (<input> | -)
  exe/itc -h | --help

Options:
  -h --help      Show this screen.
  --camel        Convert to camelCase.
  --dash         Convert to dash-case.
  --dot          Convert to dot.case.
  --pascal       Convert to PascalCase.
  --snake        Convert to snake_case.
```

You [commit](https://github.com/alcesleo/in_that_case/commit/85039bbeb03d4ac19265517efe5d94f3b1cc00f1) your changes and move on with your life.

## Epilogue / motivation

I wanted to write about exemplary code, and I think this project illustrates
what that means. Not because it's particularly good, but because it sets a very
clear example.

To add support for a new feature there was very little to
understand. Most of the time was spent mundanely copy pasting a file, going
through it and renaming stuff. None of the rest of the project had to be
touched at all, and in the end the diff is exactly adding a new, very boring
looking file, and an equally boring and obvious looking spec. Most Ruby
programmers could have probably done this in their sleep.

This kind of boring is very powerful. It is respectful of people's time. They
do not have to go digging for code that is of no interest to them, and if you
hand this task to a less experienced developer, chances are that he/she would
produce exactly the same, obvious and boring code.

One who digs a bit deeper might say that this is over engineering, that the
code is too clever, and that there is too much of it to solve such a simple
problem - and they would be right, for a project of this scale it is a
bit overkill. Here's a list of stuff that I did to make this possible:

* Dynamically require every file in the `conventions/` directory
* Automatically add all the `Convention` modules from this directory to a list of supported styles
* Derive the name of the command line arguments from the class names
* Generate a representation of each of these conventions based on their class names and their own `convert` implementation.
* Use these to dynamically generate the help text of the executable

That is a bit of extra design work, but all of it falls on me. Anyone
else wanting to touch the code in the future will have an easier time.

This is what I consider to be **exemplary code**. Even without understanding
it, it leads you down the path of making correct decisions. Writing bad code
would have been harder than writing good code. By simply existing it prevents
the code quality from decaying.

### The alternative

A big if-statement would have been faster, shorter, and probably easier to
understand at first glance - the whole project could have been one file. You
could very well argue that doing that would have been the correct decision for
a tool like this. YAGNI. If it needs to be more complex we can refactor it.
Don't do big up front design. Make the smallest/simplest thing that works. I've
made all of these arguments myself.

Imagine adding another style if the project was designed like that - it invites
legacy code. You know you should refactor it, but it's just another little
if-statement. Just like that the code which was previously a well balanced
compromise between time and functionality lays out a labyrinth of design
decisions ahead of you, even though you _just want to add this little thing_.

**Exemplary code paves the way.**
