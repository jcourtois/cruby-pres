!SLIDE bullets incremental

# :define_method

- Private method.
- Defines an instance method in self.
- Method parameter can be a Proc or Method object.
- If a block is specified, it is used as the method body.  This block is evaluated using instance_eval.

```ruby
class Person
  define_method :say_hello do
    puts "hello"
  end
end

Person.new.say_hello # => "hello"
```

For more information on blocks, procs, and lambdas, see:
http://www.robertsosinski.com/2008/12/21/understanding-ruby-blocks-procs-and-lambdas/

!SLIDE bullets

# anonymous class objects
aka DIE inheritence

```ruby
class ClasslessObject
  def self.new(attributes)
    clazz = Class.new
      attributes.keys.each do |key|
      value = attributes[key]
      callable = value
      if !value.respond_to?(:call)
        callable = lambda { || value }
      end
      clazz.send(:define_method,key,callable)
    end
    clazz.new
  end
end
```

!SLIDE

# objects like javascript!
```ruby
object = ClasslessObject.new(
  :value_per_unit => 10,
  :quantity => 3,
  :total => lambda{||value_per_unit*quantity})
object.methods - Object.new.methods
 => ["value_per_unit", "quantity", "total"]

other = ClasslessObject.new(
  :value_per_unit => 15,
  :total => lambda {|number| value_per_unit * number})
other.methods - Object.new.methods
 => ["value_per_unit", "total"]
```

!SLIDE bullets incremental

# :define_method other uses

- Grow objects.
- We'll explore this later with the class factory.
- Flat scope with closures.  (This isn't really within the scope of this talk)

!SLIDE bullets incremental

# :define_method catches

- If you have a bug, it is more difficult to locate the problem.
- Messages and stack traces are less clear.
