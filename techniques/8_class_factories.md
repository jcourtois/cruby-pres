!SLIDE bullets incremental

# class factories

- There are many methods in ruby that have significant value when metaprogramming, but whose use can also be used in normal, non-meta ways.
- This isn't a talk about their normal, non-meta ways.
- So let's delve into one final example that's about as big, bad, and meta as it gets.
- A class factory!

!SLIDE bullets incremental

# backstory

- One day, I wanted to create a game.
- The game was going to have a bunch of baddies that were all fairly generic.
- But I wanted subclasses of baddies that attacked in the same way and had similar attributes.
- BUT I didn't want to write classes for each of them.  The difference between these subclasses was a trivial matter of attribute settings! How boring!

!SLIDE

```ruby
class Baddies < BasicObject
  def self.method_missing(name, args={})
    return Baddy.new_baddy_with_attr(args.merge(name: name.to_s) )
  end

  class Baddy
    def self.new_baddy_subclass_with_attr args
      new_class = Class.new(self) do
        args.each do |attribute, value|
          define_method("#{attribute}" ) { || instance_variable_get "@#{attribute}" }
          define_method("#{attribute}=") { |new_value| instance_variable_set "@#{attribute}", new_value }
        end
        define_method("initialize") do
          args.each do |attribute, value|
            instance_variable_set("@#{attribute}", value)
          end
        end
      end
      Object.const_set args[:name].classify, new_class
      new_class
    end
  end
end
```
