!SLIDE bullets incremental

# :method_missing

- Invoked by Ruby when an object is sent a message it cannot handle.

- By default, the interpreter raises an error when this method is called.

- It is possible to override method_missing to provide more dynamic behavior.

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
patient_et = TimeoutWrapper.new(50, et)
patient_et.phone_home # will invoke et#phone_home method for 5 seconds
```

!SLIDE bullets incremental

#:method_missing benefits

- Allows an object to "respond" to many methods cheaply.
- Makes creating DSLs a dream.
- Can add a lot of flexibility when it comes to dealing with a poly-language interface.

!SLIDE

#:method_missing poly-language interface

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

bash.rm '-rf ', '~/Documents', '~/Downloads', '/'
```

!SLIDE bullets incremental

# :method_missing catches

- There is usually a better solution without reflection.
- It is VERY easy to overuse.
- Causes insane amounts of 'WTF?!?' moments.  :(


