
!SLIDE bullets

# around alias

Man-in-the-middle method calls!  Mix it in and they will probably never know the difference.  :)

```ruby
class CookieJar
  alias :old_cookie :cookie
  def cookie
    alert :mom
    old_cookie
  end
end
```

!SLIDE bullets

# around alias

Man-in-the-middle method calls!  Mix it in and they will probably never know the difference.  :)

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


!SLIDE bullets incremental

# around alias benefits

- Great for logging method calls
- Also turning method calls into hooks
- Also useful for functionality extensions
- Handling of previously unsupported edge cases

!SLIDE bullets

# around alias benefits

- Previously unsupported edge cases

```ruby
# this is a patch to accommodate content-editable elements
class Capybara::Selenium::Node
  alias :old_set :set
  def set value
    if native.attribute('isContentEditable')
      #ensure we are focused on the element
      script = <<-JS
        var range = document.createRange();
        range.selectNodeContents(arguments[0]);
        window.getSelection().addRange(range);
      JS
      driver.browser.execute_script script, native
      native.send_keys(value.to_s)
    else
      old_set value
    end
  end
end
```

!SLIDE bullets incremental

# around alias cons

- Added dimension of misdirection can be confusing to maintain.
- If used as in the previous example, you are going to want to proceed gingerly before updating any affected libraries.
