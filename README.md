C++ guidelines and good practices by Jan Wilmans (c) 2023
These guidelines are free to use by anyone, if you find them useful, please send me a (kofi)[https://ko-fi.com/janwilmans]

# guidelines

The guidelines are well, guidelines. This means nothing is set in stone.

This being say, I found that the guidelines described here are reasonble default practices.
The motto I recommend is: `comply or explain`. This means you should be able to explain in a review why you diviated from the default practice.
This might sound more harsh then it has to; I do not mean you should use the guidelines to say 'do as I say or else'. 

It's rather the other way around, guidelines should not be followed blindly!
If you are comfortable explaining why you wrote something in a way that deviated from the guidelines, then you 
demonstrate you thought about it and came to the conclusion that something else was better. And THAT IS GREAT.

# guidelines vs. style 

- With 'style' I mean the location of spaces, tabs and newlines. But also the names of variables (both are non-functional)
- With 'guidelines' I mean code changes that change behaviour of the code.

references:

- clang-format, https://clang.llvm.org/docs/ClangFormat.html 
- editorconfig, https://editorconfig.org/ 

I would like to include some suggestions to think about when chosing a style.
The whitespace in the examples below is **arbiraty** but **consistent**. I recommend chosing a writing style and enforcing it using tools such as clang-format. 
There is an example `.clang-format` file included in the repository which was used to format the examples. Also an .editorconfig file can be used to  
give you and your collegues consistent behaviour over various environments and editors.

Suggestions for chosing a style:

- Maybe conform with existing practices, if a certain style is already used for 90% of the code, the team might be used to reading this style and it might be worth configuring clang-format to
  keep it close to the currect style.
- Strive for consistency, something like: 'all type names start with a Capital' or all type names are 'snaked_cased'.
- It can be very difficult to get clang-format to do _exactly_ what _you_ want, I found that is better to go through the options, look at the result and then adopt a set of options that you find least offensive ;)
- Remember that almost all style choices will find resistance with _someone_.
- Remember that is ultimately most important to have one consistent style, because that is what gives better readability, rather then which style is chosen.
- Consider using nouns for class names and verbs for function names.

```
class Person
{
public:
    [[nodiscard]] int get_name() const;
    void set_name(int value);
private:
    int m_name;
};
```


