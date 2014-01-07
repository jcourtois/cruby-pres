!SLIDE bullets incremental

# :method_missing

- Invoked by Ruby when an object is sent a message it cannot handle.
- By default, the interpreter raises an error when this method is called.
- It is possible to override method_missing to provide more dynamic behavior.
- But if you're going to do this, you might want to...

!SLIDE

# clean your room first?

!SLIDE bullets incremental

- Since ruby 1.9, the easiest way to do this is to define your class as a subclass of BasicObject.
- BasicObject is at the top of the ruby inheritence tree.
- It doesn't even have kernel methods, like puts or sleep in scope.

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

## Example

```ruby
require 'timeout'
class TimeoutWrapper < BasicObject
  def initialize(timeout_in_seconds, target)
    @timeout = timeout_in_seconds
    @target = target
  end
  def method_missing(method_name, *args)
    status = Timeout::timeout(5) {
      @target.send(method_name, args)
    }
  end
end

et = ExtraTerrestrial.new
patient_et = TimeoutWrapper.new(5, et)
patient_et.phone_home # will invoke et#phone_home method for 5 seconds
```

!SLIDE bullets

# another timeout wrapper, this one w/ an interesting story

```ruby
class TimeBoundProxy
  def method_missing(a_method, *args, &block)
    endpoint_url = @target.endpoint_url if @target.respond_to? :endpoint_url
    measure_and_log("#{endpoint_url}:#{a_method}") do
      SystemTimer.timeout_after(@timeout) do
        @target.send a_method, *args, &block
      end
    end
  end
end
```

aside: see "Reliable Ruby timeouts with System Timer:
Do not blindly trust timeout.rb..." by Philippe Hanrigou
http://ph7spot.com/musings/system-timer

!SLIDE bullets incremental

#:method_missing benefits

- Allows an object to "respond" to many methods cheaply.
- Makes creating DSLs a dream.
- Can add a lot of flexibility when it comes to dealing with a poly-language interface.

!SLIDE

#:method_missing dsl-language interface

```ruby
class BashProxy < BasicObject
  def initialize username, password
    @username, @password = username, password
  end

  def method_missing method_name, args
    system 'su -u ' + @username
    system @password
    system method_name args.join(' ')
  end
end

bash = BashProxy.new("Admin", "admin")

bash.rm '-rf', '~/Documents', '~/Downloads', '/'
```

!SLIDE bullets incremental

# :method_missing catches

- There is usually a better solution without reflection.
- It is VERY easy to overuse.
- Causes insane amounts of 'WTF?!?' moments.  :(


