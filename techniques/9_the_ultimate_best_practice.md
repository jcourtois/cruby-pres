!SLIDE bullets incremental

# the ultimate best practice \o/

- UNIT TESTING!
- When metaprogramming, we should test both
- Our code that is writing code
- The code that our code writes.
- This is very important!

!SLIDE bullets incremental

# the ultimate best practice \o/

- In the previous class factory example, I can test:
- That my class factory is capable of making 'arbitrary' classes with 'arbitrary' attributes.
- That my class factory will not inadvertantly monkeypatch or overwrite a pre-existing class (and hopefully it notifies me if I try to do so)
- I can also test that the classes that I make using the class factory meet my expectations.
- Metaprogramming: saves on code, loads up on tests.
