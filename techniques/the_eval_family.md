!SLIDE bullets incremental

# eval, instance_eval, class_eval, module_eval

- Potentially dangerous operations.
- Evaluate Strings of Ruby code.


!SLIDE bullets

# the ruby security model

## $SAFE = 0
- No checking of the use of externally supplied (tainted) data is performed. This is Ruby's default mode.

## $SAFE >= 1
- Ruby disallows the use of tainted data by potentially dangerous operations.

## $SAFE <=> (2...4)
- Out of scope of this presentation.  :P

!SLIDE
