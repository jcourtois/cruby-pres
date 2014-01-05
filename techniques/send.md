!SLIDE content-with-caption

# :send

## Section 1

## Explanation

* Easy
* To
* Understand

## Example

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

Strategy pattern

```ruby
class Taxi
  def calculate_fare_for(distance)
    send("#{time_of_day}_rate", distance)
  end
end
```

!SLIDE bullets

# :send benefits

- Allows for dynamic method invokation.

Command pattern

```ruby
class Image
  def execute_method_sequence methods
    methods.each{ |method| self.send(method) }
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
