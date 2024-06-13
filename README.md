This is the "C++ guidelines and good practices" document by Jan Wilmans (c) 2023.

I collected these guidelines from 20 years of C++ experience and many teachings by others, honorable mentions go to [Scott Meyers](https://www.youtube.com/watch?v=wQxj20X-tIU), [Jason Turner](https://www.youtube.com/@cppweekly), [Klaus Iglberger](https://www.youtube.com/watch?v=PEcy1vYHb8A), [the Copperspice team](https://www.youtube.com/@CopperSpice), [Hana Dusíková](https://www.youtube.com/watch?v=C9MWAXYdFSY), Gert-Jan de Vos, [Anthony Williams](https://www.youtube.com/watch?v=JvHZ_OECOFU),  [STL (Stephan T. Lavavej)](https://www.youtube.com/watch?v=JhgWFYfdIho), [Peter Bindels](https://www.youtube.com/watch?v=4V9QWHjRPMc), to name a few, probably I'm forgetting many others. 

Most recently my colleagues [Adriaan de Groot](https://github.com/adriaandegroot/) and Stefan Spronkmans helped me to refine the guidelines and I owe them a big thank you!

It is likely that some of the sources of what I describe here are found in any and all conference talks since CppCon 2017. As I have spend countless hours visiting conferences and poring over the many, many videos of the talks I did not physically attend.

These guidelines are free to use by anyone, if you find them useful, please send me a [Ko-fi](https://ko-fi.com/janwilmans).

Most of the guidelines I describe here are generally considered 'Modern C++ guidelines' at the time of this writing (2023). However many of them are and were applicable regardless of whether you use C++11, C++23 or anything in between.
The guidelines are also mostly in line with Jason Turners's teachings on [C++ Weekly](https://www.youtube.com/@cppweekly). 

# Brief

The way I deviate from other sources is usually in simplification, that is, I try to give guidance even where the answer really is "it depends". My reasoning is, yes, it depends, but a go-to default is still valuable. Because the more we choose the defaults, the least surprising the code.

Also I try to keep it brief. Nobody wants to read a 200 page document before they start coding and also it would be hard to keep all of it in mind all the time.
This is why I keep the 'Highest Level Summary' below and why there is some redundancy between this document and the 'detailed document'. 


# Guidelines

The guidelines are well, guidelines. This means nothing is set in stone. 

To express this in the words of [Kate Gregory ](https://www.youtube.com/watch?v=MBRoCdtZOYg): "I kinda like having a fence, because it keeps me from falling into the river accidentally. It doesn't stop you from going in, if you want to go into the river, go into the river.". I think this is a good way to think about these guidelines as well, use them as a default and if you go beyond them, tread carefully.

This does not mean you can "not think" and blindly follow the guidelines, it means, you think about the code, apply the defaults and if that looks like it is good enough, then do not make it more complicated, because following the guidelines will make for the 'least surprising code'.

I found that the guidelines described here are reasonable default practices.
The motto I recommend is: `comply or explain`. This sounds harsh, but it is just an easy to remember motto, do not take it as an order, just as a way to remember. It means if you choose a non-default solution and deviated from a guideline, you should be able to articulate why. Also it might indicate, it is a particularly interesting piece of code so, maybe it deserve a comment to explain a 'why'. 

I do not mean you should use the guidelines to say 'do this or else' but rather the other way around, guidelines should not be followed blindly!
If you are comfortable explaining why you wrote something in a way that deviated from the guidelines, then you 
demonstrate you thought about it and came to the conclusion that something else was better. And THAT IS GREAT.

# Guidelines vs. Style 

- With 'style' I mean the placement of spaces, tabs and newlines. But also the names of types, variables, functions etc. (typically all non-functional)

- With 'guidelines' I will almost always refer to structure, language constructs, they are opinionated on what is preferred and what is not.

> The guidelines are usually good defaults, if you have no reason to deviate from them, apply them. 

Why? because they are the least surprising thing to do.
I am not claiming _these_ guidelines are the best guidelines ever. My recommendation is to take them, discuss them and form your own opinions.
The real value is having a set of guidelines for your team or ideally, for your organization, so you have shared defaults and style.

references:

- clang-format, https://clang.llvm.org/docs/ClangFormat.html 
- editorconfig, https://editorconfig.org/ 

I would like to include some suggestions to think about when choosing a style.
The whitespace in the examples below is **arbitrary** but **consistent**. I recommend choosing a writing style and enforcing it using tools such as clang-format. 
There is an example `.clang-format` file included in the repository which was used to format the examples. Also an .editorconfig file can be used to  give you and your colleagues consistent behavior over various environments and editors.

Suggestions for choosing a style:

- Consider conforming to existing practices, if a certain style is already used for 90% of the code, the team might be used to reading this style and it might be worth configuring clang-format to
  keep it close to the current style.
- It can be very difficult to get clang-format to do _exactly_ what _you_ want, I found that is better to go through the options, look at the result and then adopt a set of options that you find least offensive ;)
- Strive for consistency, prefer simple rules like: 'all type names start with a Capital' or all type names are 'snaked_cased'.
- Remember that almost all style choices will find resistance with _someone_.
- Remember that is ultimately more important to have **one consistent style**, because that is what gives better **readability**, rather then **which style** is chosen.
- Consider using nouns for class names and verbs for function names. Try to avoid abbriviations in names, they might make sense to you, but probably future readers (maybe you) will not known or remember them.

## Scope

- Do not apply your coding guidelines to third party code, it will make updates more difficult to diff and third party code is much less often read anyway.
- Do not start changing every project or function you write into 'your' style, generally, try to conform to the local style used for a project. Style changes are best made per-project or per-repository, in big chunks.
- 
## Highest Level Summary

Generally, use the most fitting tool for the job, if it has less features, it allows for less mistakes.

-   Do not use C-style casts. [meme](https://github.com/janwilmans/guidelines/assets/5933444/27784daa-1ed8-4d75-9482-0e3e2be1aae7)
-   Initialize all variables at declaration. [meme](https://github.com/janwilmans/guidelines/assets/5933444/4592cf74-7957-46e8-8133-0d065bab56d8)
-   Use `const` and `nodiscard` whenever you can (but no const for member variables). [meme](https://github.com/janwilmans/guidelines/assets/5933444/e1f32720-76e9-41d2-a2cd-c7167a6fe881)
-   Keep scope as limited as possible.
-   No owning raw pointers.
-   No manual memory management using `new`, `delete`, `malloc`, `free`, etc.
    -   When working with Qt the use of the new keyword explicitly allowed.
-   Do not use `volatile`, `const_cast` or `reinterpret_cast`, `typedef`, `register` or `extern`.
-   Make all destructors of classes used in runtime polymorphism virtual.
-   Do not add member variables to classes used as interfaces. (Interfaces are defined as pure virtual classes that have a virtual = default destructor)

That's all folks! As you can see, C++ is easy ;) as long as you KISS.

Well there are [more details](guidelines_details.md) but I think if you follow the above guidelines, you are well on your way to writing decent code!
You can see that the `guidelines` are quite 'bold', like 'do not use reinterpret_cast'. 
And I can hear you think "but, sometimes". 

EXACTLY: **sometimes** you need it.

and that is why these are guidelines and not set in stone restrictions.
If you can explain (to yourself): yes, I see the guidelines, but this case is special, then by all means, deviate, however, make it explicit.
You may consider wrapping that `reinterpret_cast` you need into a function and let the name explain the purpose or the 'why'.

Finally, I invite you to read and study the [details](guidelines_details.md), let me know if you agree, disagree, have suggestions for improvements, 
this document is alive and I'm sure will change for the better over time, let's make it better together!

I'm looking forward to hearing from all of you!

-- Jan
