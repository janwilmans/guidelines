This is the "C++ guidelines and good practices" document by Jan Wilmans (c) 2023.
I collected these guidelines from 20 years of C++ experience and many teachings by others, honorable mentions go to [Scott Meijers](https://www.youtube.com/watch?v=wQxj20X-tIU), [Jason Turner](https://www.youtube.com/@cppweekly), [Klaus Iglberger](https://www.youtube.com/watch?v=PEcy1vYHb8A), [the Copperspice team](https://www.youtube.com/@CopperSpice), [Hana Dusíková](https://www.youtube.com/watch?v=C9MWAXYdFSY), Gert-Jan de Vos, [Anthony Williams](https://www.youtube.com/watch?v=JvHZ_OECOFU) and [STL (Stephan T. Lavavej)](https://www.youtube.com/watch?v=JhgWFYfdIho) to name a few, probably I'm forgetting many others.

It is likely that some of the sources of what I describe here are found in any and all conference talks since CppCon, 16 Oct 2017. As I have spend countless hours visiting conferences and poring over the many, many videos of the talk I did not physically attend.

These guidelines are free to use by anyone, if you find them useful, please send me a [Ko-fi](https://ko-fi.com/janwilmans).

Most of the guidelines I describe here are generally considered 'Modern C++ guidelines' at the time of it's writing (2023). However many of them are and were applicable regardless of whether you use C++11, C++23 or anything in between.
The guidelines are also mostly in line with Jason Turners's teachings on [C++ Weekly](https://www.youtube.com/@cppweekly). 

The way I deviate from other sources is usually in simplification, that is, I try to give guidance even where the answer really is "it depends". My reasoning is, yes, it depends, but a go-to default is still valuable. Because the more we choose the defaults, the least surprising the code.

# guidelines

The guidelines are well, guidelines. This means nothing is set in stone. 

To express this in the words of [Kate Gregory ](https://www.youtube.com/watch?v=MBRoCdtZOYg). "I kinda like having a fence, because it keeps me from falling into the river accidentally. It doesn't stop you from going in, if you want to go into the river, go into the river.". I think this is a good way to think about these guidelines, use them as a default and if go beyond them, treat carefully.

This does not mean you can "not think" and blindly follow the guidelines, it means, you think about the code, apply the defaults and if that looks like it is good enough, then do not make it more complicated, because following the guidelines will make for the 'least surprising code'.

I found that the guidelines described here are reasonable default practices.
The motto I recommend is: `comply or explain`. This means if you chose a non-default solution, and divided from a guidelines, you should be able to explain in a review why. Also if might indicate, this is a particularly interesting piece of code so, maybe it deserves a comment to explain this. This might sound more harsh then it has to; I do not mean you should use the guidelines to say 'do as I say or else'. 

It's rather the other way around, guidelines should not be followed blindly!
If you are comfortable explaining why you wrote something in a way that deviated from the guidelines, then you 
demonstrate you thought about it and came to the conclusion that something else was better. And THAT IS GREAT.

# guidelines vs. style 

- With 'style' I mean the location of spaces, tabs and newlines. But also the names of types, variables, functions etc. (typically all non-functional)
- With 'guidelines' I will almost always refer to changes that affect the behavior of the code.

references:

- clang-format, https://clang.llvm.org/docs/ClangFormat.html 
- editorconfig, https://editorconfig.org/ 

I would like to include some suggestions to think about when choosing a style.
The whitespace in the examples below is **arbitrary** but **consistent**. I recommend choosing a writing style and enforcing it using tools such as clang-format. 
There is an example `.clang-format` file included in the repository which was used to format the examples. Also an .editorconfig file can be used to  
give you and your colleagues consistent behavior over various environments and editors.

Suggestions for choosing a style:

- Consider conforming to existing practices, if a certain style is already used for 90% of the code, the team might be used to reading this style and it might be worth configuring clang-format to
  keep it close to the current style.
- It can be very difficult to get clang-format to do _exactly_ what _you_ want, I found that is better to go through the options, look at the result and then adopt a set of options that you find least offensive ;)
- Strive for consistency, prefer simple rules like: 'all type names start with a Capital' or all type names are 'snaked_cased'.
- Remember that almost all style choices will find resistance with _someone_.
- Remember that is ultimately more important to have **one consistent style**, because that is what gives better **readability**, rather then **which style** is chosen.
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


