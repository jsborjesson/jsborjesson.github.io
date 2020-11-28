---
layout: post
title: Implementing equality in Ruby
---

Equality in Ruby is, at first glance, deceptively simple. Overriding the `==` method will get you almost all the way there, but if you want your objects to be consistently good citizens of Ruby-land, you need to think of a few more things.

## Contents

* [Skeleton](#skeleton)
* [`==`](#==)
* [`equal?`](#equal)
* [`hash`](#hash)
* [`eql?`](#eql)
* [`<=>`](#<=>)
* [Full example](#full-example)

## Skeleton

We will be implementing equality on a class called `T`, it simply holds a value and doesn't reveal it back to us.

```ruby
class T
  attr_reader :value
  private :value

  def initialize(value)
    @value = value
  end
end
```

Our test harness is equally simple:

```ruby
require "minitest/autorun"

class EqualityTest < Minitest::Test
  def test_equality
    assert_equal T.new(1), T.new(1)
    refute_equal T.new(1), T.new(2)
  end
end
```

From now on I'll show only snippets of what we change, the complete example is shown at the end.

<h2 id="==">==</h2>

To make this test pass we need to implement the `==` operator

```ruby
def ==(other)
  self.value == other.value
end
```

This gives us an error:

```
NoMethodError: private method `value' called for #<T:0x007f8c293b21a0 @value=1>
```

Since `value` is private, we cannot access it on a different instance, even if we are inside the same class. To fix this, we set it to `protected` instead. This means that we can look at other `T` instances' `value` from inside `T`, but it will still not reveal it to anyone else.

```ruby
protected :value
```

Now our test is passing.

### Edge cases

Our implementations covers the most basic case, but it's not quite enough. Consider the following test cases:

```ruby
refute_equal T.new(1), Object.new
refute_equal T.new(1), nil
```

Both of these will break with an error like this:

```
NoMethodError: undefined method `value' for #<Object:0x007ff51916df58>
```

Let's adapt our `==` implementation to account for these edge cases:

```ruby
def ==(other)
  return false unless other.is_a?(self.class)
  self.value == other.value
end
```

Now we have a pretty solid implementation of equality.

## equal?

`equal?` determines if two objects are the **exact same instance**. You should never override this.

```ruby

t_one = T.new(1)

t_one == T.new(1)      # => true
t_one == t_one         # => true
t_one.equal?(T.new(1)) # => false
t_one.equal?(t_one)    # => true
```

## hash

Two equal objects should have the same hash code.

```ruby
def test_hash
  assert_equal T.new(1).hash, T.new(1).hash
  refute_equal T.new(1).hash, T.new(2).hash
end
```

In our simple example we can simply delegate the hash method to our value:

```ruby
def hash
  value.hash
end
```

_When you have more instance variables, an easy way to implement `hash` is to offload the work to `Array` which already has a convenient implementation for this:_

```ruby
# Not needed in this simple example
def hash
  [value1, value2].hash
end
```

## eql?

In Ruby, you can use any object as the key in a `Hash` (note the capital H).

```ruby
h = { "key" => "value" }
h["key"] # => "value"

"key" == "key"           # => true (they have the same value)
"key".hash == "key".hash # => true (they have the same hash code)
"key".equal?("key")      # => false (but they are not the same instance of String)
```

Let's make sure our `T` behaves the same way.

```ruby
def test_hash_key_access
  h = { T.new(1) => :val }
  assert_equal :val, h[T.new(1)]
end
```

It seems something is still missing from our implementation:

```
  1) Failure:
EqualityTest#test_hash_key_access [equality.rb:37]:
Expected: :val
  Actual: nil
```

Turns out, `Hash` uses both the `hash` method, and the `eql?` method to determine which value to retrieve. To fix this, all we need to do is alias our `==` implementation to `eql?`:

[Reference](https://docs.ruby-lang.org/en/2.4.0/Hash.html#class-Hash-label-Hash+Keys)

```ruby
def ==(other)
  return false unless other.is_a?(self.class)
  self.value == other.value
end
alias eql? ==
```

And our tests are passing again.

<h2 id="<=>"><=></h2>

If we want our `T`s to be comparable with other `T`s, we can implement comparison operators. Let's write a test first:

```ruby
def test_comparisons
  assert_operator T.new(1), :<, T.new(2)
  refute_operator T.new(2), :<, T.new(1)
end
```

This is easy enough and looks a lot like our `==` implementation.

```ruby
def <(other)
  self.value < other.value
end
```

Unlike our `==`, which we want to simply say "no" when compared with something completely different, we want `<` to raise an error, because the comparison simply doesn't make sense. We cannot answer yes or no to whether a `T` is smaller than `nil` for example:

```ruby
assert_raises(ArgumentError) { T.new(1) < nil }
assert_raises(ArgumentError) { T.new(1) < 1 }
assert_raises(ArgumentError) { T.new(1) < Object.new }
```

Though slightly different, the solution to this looks a lot like it did in `==`:

```ruby
def <(other)
  fail ArgumentError unless other.is_a?(self.class)
  self.value < other.value
end
```

Now all of our tests are passing again. We could go on and implement `>` the same way, but it quickly gets ridiculous once you get to `<=` and `>=`.

```ruby
def test_comparisons
  assert_operator T.new(1), :<, T.new(2)
  refute_operator T.new(2), :<, T.new(1)
  assert_operator T.new(2), :>, T.new(1)
  refute_operator T.new(1), :>, T.new(2)
  assert_operator T.new(1), :<=, T.new(2)
  assert_operator T.new(1), :<=, T.new(1)
  refute_operator T.new(2), :<=, T.new(1)
  assert_operator T.new(2), :>=, T.new(1)
  assert_operator T.new(1), :>=, T.new(1)
  refute_operator T.new(1), :>=, T.new(2)

  assert_raises(ArgumentError) { T.new(1) < 1 }
  assert_raises(ArgumentError) { T.new(1) > Object.new }
  assert_raises(ArgumentError) { T.new(1) >= nil }
end
```

Thankfully, Ruby has a good answer for this: `Comparable`.

```ruby
include Comparable
```

### The contract of Comparable

`Comparable` needs you to implement _the spaceship operator_, which looks like this: `<=>`. This method has a contract that says essentially:

> "Return 0 if the two objects are the same, return -1 if the first is smaller, return 1 if the first is bigger, and return nil if they cannot be compared".

You can try this out yourself:

```ruby
1 <=> 1          # => 0
1 <=> 2          # => -1
1 <=> 0          # => 1
1 <=> Object.new # => nil
```

### Implementing spaceship

Let's replace our `==` and `<` implementation with `<=>`:

```ruby
def <=>(other)
  return nil unless other.is_a?(self.class)
  self.value <=> other.value
end
alias eql? ==
```

Now all of our tests are passing!

We delegate our spaceship operator to the `value`s just like in `==`, and we return `nil` in case the types don't match. This follows the Spaceship Contract.

There are a few things to take note of here:

* We still need to alias `eql?` to `==`, although now `==` is being implemented in `Comparable` so we can't see it.
* Our `==` still returns `false` when compared to something strange, and our `<` still raises an `ArgumentError`. `Comparable` is smart like that.

## Full example

Now we have a class that behaves well in every equality situation in Ruby-land. Did I miss anything? Please [open an issue](https://github.com/jsborjesson/jsborjesson.github.io/issues/new).

```ruby
class T
  include Comparable

  attr_reader :value
  protected :value

  def initialize(value)
    @value = value
  end

  def <=>(other)
    return nil unless other.is_a?(self.class)
    self.value <=> other.value
  end
  alias eql? ==

  def hash
    value.hash
  end
end

require "minitest/autorun"

class EqualityTest < Minitest::Test
  def test_equality
    assert_equal T.new(1), T.new(1)
    refute_equal T.new(1), T.new(2)
    refute_equal T.new(1), Object.new
    refute_equal T.new(1), nil
  end

  def test_hash
    assert_equal T.new(1).hash, T.new(1).hash
    refute_equal T.new(1).hash, T.new(2).hash
  end

  def test_hash_key_access
    h = { T.new(1) => :val }
    assert_equal :val, h[T.new(1)]
  end

  def test_comparisons
    assert_operator T.new(1), :<, T.new(2)
    refute_operator T.new(2), :<, T.new(1)
    assert_operator T.new(2), :>, T.new(1)
    refute_operator T.new(1), :>, T.new(2)
    assert_operator T.new(1), :<=, T.new(2)
    assert_operator T.new(1), :<=, T.new(1)
    refute_operator T.new(2), :<=, T.new(1)
    assert_operator T.new(2), :>=, T.new(1)
    assert_operator T.new(1), :>=, T.new(1)
    refute_operator T.new(1), :>=, T.new(2)

    assert_raises(ArgumentError) { T.new(1) < 1 }
    assert_raises(ArgumentError) { T.new(1) > Object.new }
    assert_raises(ArgumentError) { T.new(1) >= nil }
  end
end
```
