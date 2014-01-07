!SLIDE

# opening classes, monkey-patching

```ruby
class Integer
  def to_roman
    # The answer
    'XLII' # Guaranteed to NOT be random
  end
end
```

```ruby
class RSpec::Core::Example
  def passed?
    @exception.nil?
  end
  def failed?
    !passed?
  end
end
```

!SLIDE bullets incremental

# open classes/modules benefits

- Allows for extensions (e.g. useful methods that are lacking or for gems that are no longer maintained)
- Can save you from being stuck in older versions
- Allows the 'slow' evolution of your system with backports

!SLIDE bullets incremental

# open classes/modules catches

- Causes many 'WTF' moments.  Where is 'insert your concern here' being modified?
- After you monkey-patch a gem, you are probably not going to want to update it.
- Ages very badly with different implementation details.
- Any dependencies you create will be very hard to clean up later.
- Unintentional side-effects.  \*shudder\*

!SLIDE bullets incremental

# be especially careful when patching core classes
## cache money example

```ruby
class Array
  alias_method :count, :size

  def to_hash
    keys_and_values_without_nils = reject { |key, value| value.nil? }
    shallow_flattened_keys_and_values_without_nils = \
             keys_and_values_without_nils.inject([]) \
                   { |result, pair| result += pair }
    Hash[*shallow_flattened_keys_and_values_without_nils]
  end
end
```

- Doesn't seem like a big deal, right?

!SLIDE bullets incremental

An example from rails 3.

```ruby
def find_by_attributes(match, attributes, *args)
  conditions = Hash[attributes.map {|a| [a, args[attributes.index(a)]]}]
  result = where(conditions).send(match.finder)

  if match.bang? && result.blank?
    raise RecordNotFound, "Couldn't find #{@klass.name} with " << \
         "#{conditions.to_a.collect {|p| p.join(' = ')}.join(', ')}"
  else
    result
  end
end
# from vendor/bundle/ruby/1.8/gems/activerecord-3.0.20/
# ... /lib/active_record/relation/finder_methods.rb
```

- When we upgraded to rails 3, we started seeing MissingAttribute exceptions whenvever we used joins.
- This was because rails 3 was using Hash[] syntax to initialize models from query results.
- Also, note that this "to_h" method wasn't named very well.

!SLIDE bullets incremental

# a couple of scenarios:

- If it's the ruby core, don't do it.  Please.  Just don't.
- If it's someone else's tool, then you should submit a pull request

!SLIDE bullets incremental

# it's not just your best practices.  it's also a matter of those used in the gems in your projects.

## hypothetical situation1:
- "We start using this gem, we're not aware that it's overriding any core ruby functionality and we end up suffering for it."
## hypothetical situation2:
- "We're using this gem that we KNOW is overriding core functionality.  Whether we intended to or not, we started interlacing dependencies in our projects.  Now we want to REMOVE this gem."

!SLIDE bullets incremental

# but... how do we monkeypatch the right way?

- \*crickets\*

!SLIDE bullets incremental

# best practices, given you're going to do this

- Say you want to define *foo* in *obj*
- Ruby has a bunch of great methods like object#respond_to?, module#instance_methods, and module#private_instance_methods.
- These can be used to suss out whether obj.foo is already defined.
- If it is you should not feel comfortable monkeypatching your own foo in.
- If it is already defined: maybe you'd like to try around alias?
- If it's not... ?

!SLIDE bullets

# use self-deprecating extensions

```ruby
def self.included(klass)
  klass.class_eval do
    alias_method_chain :stub, :save_original
    alias_method_chain :unstub, :restore_original
  end
end

def should_intercept?
  RUBY_VERSION < '1.9' && mocha_will_remove_method?
end
```

- Mocha 0.13.3 has a bug with ruby 1.8.7, so we wrote an extension that will not do anything if we are running ruby 1.9 or above.

!SLIDE bullets

# mixin your monkeypatches!

What is the difference between:

```ruby
class String
  def palindrome?
    self == self.reverse
  end
end
```

```ruby
module JimmysPalindromeFinder
  def palindrome?
    return false unless self.respond_to? :reverse
    self == self.reverse
  end
end

class String
  include JimmysPalindromeFinder
end
```

!SLIDE

# the first case:
```ruby
class String
  def palindrome?
    self == self.reverse
  end
end
```

```ruby
String.ancestors # => [String, Comparable, Object, Kernel, BasicObject]
```

!SLIDE

# the second:

```ruby
module JimmysPalindromeFinder
  def palindrome?
    return false unless self.respond_to? :reverse
    self == self.reverse
  end
end

class String
  include JimmysPalindromeFinder
end
```

```ruby
String.ancestors
# => [String, JimmysPalindromeFinder, Comparable, Object, Kernel, BasicObject]
```

!SLIDE bullets incremental

# james' takeaway

- It is not really possible to be responsible with monkeypatching
- If you're going to do it, it's important to be prudent.  Look before you patch!
- Transparency is another important goal.  Prefer mixins to simple patches
- Core ruby classes are not a very good target
- Prefer self-deprecating patches
- Name your patches well (and specifically)
- Be careful.  And fix the versions of your gems in your Gemfile

!SLIDE bullets incremental

# hugo's takeaway

