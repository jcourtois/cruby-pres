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
