!SLIDE bullets incremental

# the ultimate best practice \o/

- UNIT TESTING!
- When metaprogramming, we should test both
- Which pieces get changed
- How they get changed
- This is very important!

!SLIDE bullets incremental

# why test?

- If we modify it, we know it's still working.
- Tests are really important in terms of documentation.
- Metaprogramming is often hard to read.

!SLIDE bullets incremental

# the ultimate best practice \o/

- In the previous class factory example, I can test:
- That my class factory is capable of making 'arbitrary' classes with 'arbitrary' attributes.
- That my class factory will not inadvertantly monkeypatch or overwrite a pre-existing class (and hopefully it notifies me if I try to do so)
- I can also test that the classes that I make using the class factory meet my expectations.
- Metaprogramming: saves on code, loads up on tests.

!SLIDE

# questions?

!SLIDE

# thank you for coming out!
