!SLIDE bullets incremental

# :method_missing

- Invoked by Ruby when an object is sent a message it cannot handle.
- By default, the interpreter raises an error when this method is called.
- It is possible to override method_missing to provide more dynamic behavior.
- But if you're going to do this, you might want to...


!SLIDE

# :method_missing

## Example

```ruby
require 'timeout'
class TimeoutWrapper
  def initialize(timeout_in_seconds, target)
    @timeout = timeout_in_seconds
    @target = target
  end
  def method_missing(method_name, *args)
    status = Timeout::timeout(@timeout) {
      @target.send(method_name, args)
    }
  end
end
class ExtraTerrestrial < BasicObject; end
et = ExtraTerrestrial.new
patient_et = TimeoutWrapper.new(5, et)
et.methods # => <Error: undefined method "methods">
patient_et.methods # => [:method_missing, :nil?, :methods, ... ]
```
!SLIDE bullets incremental

# clean your room first?

- Since ruby 1.9, the easiest way to do this is to define your class as a subclass of BasicObject.
- BasicObject is at the top of the ruby inheritence tree.
- It doesn't even have kernel methods, like puts or sleep in scope.

!SLIDE

# for all of you fans of ruby <1.9

```ruby
# another option
class MyCleanRoom
  instance_methods.each do |m|
     undef_method m unless m.to_s =~ /^__|method_missing|respond_to?/
  end
end
```


!SLIDE

# :method_missing

## Fixed example

```ruby
require 'timeout'
class TimeoutWrapper < BasicObject
  def initialize(timeout_in_seconds, target)
    @timeout = timeout_in_seconds
    @target = target
  end
  def method_missing(method_name, *args)
    status = Timeout::timeout(@timeout) {
      @target.send(method_name, args)
    }
  end
end

et = ExtraTerrestrial.new
patient_et = TimeoutWrapper.new(5, et)
patient_et.phone_home # will invoke et#phone_home method for 5 seconds
```

!SLIDE bullets

# overrided method_missing
```ruby
class Product < ActiveRecord::Base
 def method_missing method_name, *args
   if(method_name.to_s =~ /^print_/)
     puts send(method_name.to_s[6..-1])
   else
     super
   end
 end
end
p=Product.new(:name => "My Awesome Product")
p.print_name # => "My Awesome Product"
p.send(:print_name) # => "My Awesome Product"
# good, but....
p.respond_to?(:print_name) # => false  (!?)
p.method(:print_name) # => <throws NameError: undefined method> (!?)
# how to fix this!?!?
```
!SLIDE bullets

# override method_missing, define responds_to_missing?
```ruby
class Product < ActiveRecord::Base
 def method_missing method_name, *args
   if(method_name.to_s =~ /^print_/)
     puts send(method_name.to_s[6..-1])
   else
     super
   end
 end
 def respond_to_missing?(method_name, include_private=false)
   !(method_name.to_s =~ /^print_/).nil? || super
 end
end
p=Product.new(:name => "My Awesome Product")
p.print_name # => "My Awesome Product"
p.send(:print_name) # => "My Awesome Product"
p.respond_to?(:print_name) # => true
p.method(:print_name) # => <Method: ...>
```

!SLIDE bullets incremental

#:method_missing benefits

- Allows an object to "respond" to many methods cheaply.
- Makes creating DSLs a dream.
- Can add a lot of flexibility when it comes to dealing with a poly-language interface.

!SLIDE bullets incremental

# :method_missing catches

- There is usually a better solution without reflection.
- It is VERY easy to overuse.
- Causes insane amounts of 'WTF?!?' moments.  :(


