!SLIDE bullets incremental

# eval, instance_eval, module_eval, class_eval

- Evaluate Strings of Ruby code in the context of the receiving object.
- You can do just about whatever you want with this.
- Downsides?

!SLIDE bullets incremental
# reasons to be wary of the :eval_family

- Strings of code are not syntax-highlighted.
- Syntax errors within the Strings will not register until they are evaluated.
- These are "potentially dangerous operations."
- What do we mean by "potentially dangerous operations"?

!SLIDE bullets

# the ruby security model

## $SAFE == 0
- No checking of the use of externally supplied (tainted) data is performed. This is Ruby's default mode.

## $SAFE >= 1
- Ruby disallows the use of tainted data by potentially dangerous operations.

## $SAFE === (2...4)

- Out of scope of this presentation.  :P

!SLIDE bullets incremental

#What can we do to manage security?

- Manage safety!  $SAFE= 1 in production is preferable, though some great tools need $SAFE=0 to work.  Mostly pry comes to mind.
- Make sure external inputs are marked tainted, then sanitized before they are trusted.
- Object#taint, Object#tainted?, Object#untaint are methods built just for this purpose.
- Avoid :evaling code that you did not write.  It's just prudent.
- But if you must use the eval family...

!SLIDE

# eval_family best practice:

*Use the eval family with positioning information :)*

You will get a better stack trace in the case of an exception.

```ruby
eval("puts 'hello world'")
# becomes
eval("puts 'hello world'", binding, __FILE__, __LINE__)

String.module_eval("A=1")
# becomes
String.module_eval("A=1", binding, __FILE__, __LINE__)

"str".instance_eval("puts self")
# becomes
"str".instance_eval("puts self", binding, __FILE__, __LINE__)
```

Credit: Ola Bini
https://olabini.com/blog/2008/01/ruby-antipattern-using-eval-without-positioning-information/

!SLIDE bullet incremental

# eval family summary

- Even then, many programmers avoid the :eval family.
- The :exec family and :send offer more reliable ways of achieving similar ends.
- But if you're going to ... (1) do it safely and (2) add positioning information to your :eval calls!
