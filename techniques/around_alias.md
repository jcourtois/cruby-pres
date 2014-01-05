!SLIDE bullets

# around alias

- Man-in-the-middle method calls!  :)

```ruby
module DSL
  alias :old_on_page_with :on_page_with

  def on_page_with(*module_names)
    old_on_page_with(*module_names){ |page| @page=page; yield page }
  end

  define_method :step do |step_number, action_name, args={}|

    if step_number.is_a? Fixnum
      example.metadata[:doc_steps].store step_number, action_name
    end

    @page.perform action_name, args
  end
end
```
