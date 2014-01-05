!SLIDE bullet incremental

# class_exec, module_exec, instance_exec*

- This family allows you to execute blocks of code within the receiver.
- In many cases, the *_exec family is a great alternative to the eval family.
- While Kernal.eval is a member of the eval family, Kernal.exec is *not* a member of the exec family.

!SLIDE comparison

# difference between :send, the :*evals, and the :*_execs

## :execs

- Executes a block of ruby code within the reciever
- Has access to private attributes and methods
- Not "potentially dangerous" (will process tainted input at $SAFE>0)

## :evals

- Executes a String of ruby code *or* a block of ruby code within the the receiver
- Has access to private attributes and methods
- "Potentially dangerous" (will not be able to process tainted input at $SAFE>0)

## :send

- Executes a String or symbol as a method call within the receiver
- Has access to private methods, but not attributes
- Not "potentially dangerous".  This surprised me, sort of
