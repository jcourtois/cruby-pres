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

!SLIDE bullets

# :define_method uses

- Using define_method will maintain flat scope, e.g. when writing mixins.

```ruby
module RspecErrorReport
  define_method :log_information_on do |object|
    example[:exceptional_object] = object
  end
end
```

```ruby
include 'RspecErrorReport'
describe 'AAA' do
  it 'aaa' do
    begin
      obj.foo
    rescue
      log_information_on obj
    end
  end
end
```

!SLIDE bullets incremental

# :define_method other uses

- 'Grow' objects.

```ruby
def my_class_maker args={}
  Class.new do

  end
end
```

!SLIDE bullets incremental

# :define_method catches

- If you have a bug, it is more difficult to locate the problem.
- Messages and stack traces are less clear.
