!SLIDE bullets

# :send

- Invokes the method identified by :symbol, passing it any additional arguments specified.

```ruby
class Trip < ActiveRecord::Base  # Crappy backport of 'where' to rails 2.3
  def self.where(attribute_map)
    keys = attribute_map.keys
    values = keys.map{|key|attribute_map[key]}
    composed_name = keys.map(&:to_s).join('_and_')
    send("find_by_#{composed_name}", values)
  end
endTrip.where(:origin => 'ORD', :destination => 'SFO')
```
!SLIDE incremental bullets
!SLIDE bullets

# :send benefits

- Allows for dynamic method invokation.

```ruby
class Taxi
  def calculate_fare_for(distance)
    send("#{time_of_day}_rate", distance)
  end
  def rush_hour_rate
    # ...
  end
  def evening_rate
    # ...
  end
  def afternoon_rate
    # ...
  end
end
```

!SLIDE bullets

# :send benefits

- Allows for dynamic method invokation.

```ruby
class Image
  def execute_method_sequence methods
    methods.each{ |method| self.send(method) }
  end
  def rotate_90
    # ...
  end
  def h_flip
    # ...
  end
end

kitty1, kitty2 = Image.new, Image.new

messed_up_kitty = kitty1.execute_method_sequence ['rotate_90', 'h_flip']
different_kitty = kitty2.execute_method_sequence ['h_flip', 'rotate_90']
```

!SLIDE incremental bullets

# other :send benefits

- Simplifies creating your own DSLs (Domain-specific languages)
- A 'lighter' version of :eval limited to method calls within an object
- Ability to externally invoke private methods can help in testing. (!)

!SLIDE incremental bullets

# :send catches

- Difficult to determine which methods need to exist for :send to work
- Difficult to follow the code path; you may end up debugging more often.  :(
- When :send is invoked with user input, may cause security issues.
- Difficult to refactor!

!SLIDE bullets incremental

# when should i use/ not use :send?

## ok excuse
- Only use this if you can't know which method you'd like to call at coding time.  See the Timeout example.

## bad excuses

- Too lazy to type
- Don't want to call two methods in a row

```ruby
args = #...
method_list = ['validate', 'send_email_with']
method_list.each{ |method| send method, args }
# :(
```
