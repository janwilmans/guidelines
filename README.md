# C++ Default Guidelines

This is a C++ guidelines and good practices document by Jan Wilmans (c) 2023

The main purpose for this set of guidelines is to serve as a starting point for people that are new to the language or learners seeking to improve their C++ writing quality.

It can also be used as a starting point for making your own guidelines for a project.

I collected these guidelines from 20 years of C++ experience and many teachings by others, honorable mentions go to [Scott Meyers](https://www.youtube.com/watch?v=wQxj20X-tIU), [Jason Turner](https://www.youtube.com/@cppweekly), [Klaus Iglberger](https://www.youtube.com/watch?v=PEcy1vYHb8A), [the Copperspice team](https://www.youtube.com/@CopperSpice), [Hana Dusíková](https://www.youtube.com/watch?v=C9MWAXYdFSY), Gert-Jan de Vos, [Anthony Williams](https://www.youtube.com/watch?v=JvHZ_OECOFU),  [STL (Stephan T. Lavavej)](https://www.youtube.com/watch?v=JhgWFYfdIho), [Peter Bindels](https://www.youtube.com/watch?v=4V9QWHjRPMc), [Sy Brand](https://www.youtube.com/watch?v=MZo7k_IOCe8) to name a few, probably I'm forgetting many others. 

Most recently my colleagues [Adriaan de Groot](https://github.com/adriaandegroot/) and Stefan Spronkmans helped me to refine the guidelines and I owe them a big thank you!

It is likely that the sources of what I describe here are found in any and all conference talks since CppCon 2017. As I have spend countless hours visiting conferences and poring over the many, many videos of the talks I did not physically attend.

These guidelines are free to use by anyone, if you find them useful, I would appreciate your support through a [Ko-fi](https://ko-fi.com/janwilmans).

Most of the guidelines I describe here are generally considered 'Modern C++ guidelines' as of 2023. However, many of them are applicable regardless of whether you use C++11, C++23 or anything in between.
The guidelines are also mostly in line with Jason Turners's teachings on [C++ Weekly](https://www.youtube.com/@cppweekly). Also I added references to corresponding C++ Core Guidelines where applicable.

# Brief

In some places you may find my guidelines contradict common advice, this is unavoidable and also intential. My approach typically deviates from other sources through simplification. I try to offer guidance even when the answer is "it depends." My reasoning is that, while it does depend, having a go-to default is valuable. The more we choose these defaults, the less surprising the code becomes.

I also strive to keep it brief. Nobody wants to read a 200-page document before they start coding, and it's difficult to keep all of that in mind all the time. This is why I provide the [Highest Level Summary](#highest-level-summary) below and why there is some redundancy between [this](README.md) document and the [detailed document](guidelines_details.md).

# Guidelines

These guidelines are just that, guidelines. They are not rules.

The difference is, guidelines are suggestions that should be considered, but its ok to deviate from them. Rules imply more rigidity and should not be broken. (if you aim to follow good practices that is).

To express this in the words of [Kate Gregory ](https://www.youtube.com/watch?v=MBRoCdtZOYg): "I kinda like having a fence, because it keeps me from falling into the river accidentally. It doesn't stop you from going in, if you want to go into the river, go into the river." I think this is a good way to think about guidelines as well, use them as a default and if you go beyond them, tread carefully.

I should also mention you should not follow guidelines mindlessly. Instead, think about the code, apply the defaults and if they work well, don't complicate things any further. Following these default guidelines typically results in the least surprising code.

I find that the guidelines outlined here are reasonable default practices for many projects. The motto I recommend is: **comply or explain**. While it might sound harsh, it is just an easy to remember motto, do not take it as an order, just as a way to remember. It means if you choose a non-default solution and deviated from a guideline, you should be able to articulate why. Such instances may indicate particularly interesting code, that you could move into a separate function. Or could benefit from a comment explaining a 'why'.

To re-affirm, do not to use these guidelines as a strict "do this or else" directive but rather the other way around, the guidelines should never be followed blindly! If you are comfortable explaining why you wrote something in a way that deviated from the guidelines, then you demonstrate you thought about it and came to the conclusion that something else was better. And THAT IS GREAT.

# Guidelines vs. Style 

- With 'style' I mean the placement of spaces, tabs and newlines. But also the names of types, variables, functions etc. (typically all non-functional)

- With 'guidelines' I will almost always refer to structure, language constructs, they are opinionated on what is preferred and what is not.

The real value is having a set of guidelines for your team or ideally, for your organization, so you have shared defaults and style.

references:

- clang-format, https://clang.llvm.org/docs/ClangFormat.html 
- editorconfig, https://editorconfig.org/ 

The whitespace in the examples below is **arbitrary** but **consistent**. I recommend choosing a writing style and enforcing it using tools such as clang-format. 
There is an example `.clang-format` file included in the repository which was used to format the examples. Also an .editorconfig file can be used to  give you and your colleagues consistent behavior over various environments and editors.

Suggestions for choosing a style:

- Consider conforming to existing practices, if a certain style is already used for 90% of the code, the team is likely accustomed to reading this style. It might be worthwhile to configure clang-format to keep it close to the current style.
- It can be very difficult to get clang-format to do _exactly_ what _you_ want, I found that is better to go through the options, look at the result and then adopt a set of options that you find least offensive ;)
- Strive for consistency. Prefer simple rules like: 'all type names start with a capital letter' or all type names are 'snake_cased'.
- Remember that almost all style choices will find resistance with _someone_.
- Ultimately it is more important to have **one consistent style**, because that is what enhances **readability**, rather then **which style** is chosen.
- Consider using nouns for class names and verbs for function names. Try to avoid abbreviations in names; they might make sense to you, but  future readers (including yourself) might not understand or remember them.

## Scope

- Do not apply your coding guidelines to third party code, it will make updates more difficult to diff and third party code is much less often read anyway.
- Do not start changing every project or function you write into 'your' style, generally, try to conform to the local style used for a project. Style changes are best made per-project or per-repository, in big chunks.

## Highest Level Summary

Generally, use the most fitting tool for the job, if it has less features, it allows for less mistakes.

- [H.1] Turn on warnings! and use sanitizers [[P.12]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#p12-use-supporting-tools-as-appropriate) (see [details](warnings.md) here)
- [H.2] Avoid global mutable state [[I.2]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#Ri-global) [[I.3]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#Ri-singleton)
- [H.3] Keep the scope of variables and type declarations as limited as possible. [examples](examples.md#keep-scope-as-limited-as-possible)
- [H.4] Initialize all variables at declaration. [[meme]](https://github.com/janwilmans/guidelines/assets/5933444/4592cf74-7957-46e8-8133-0d065bab56d8)
- [H.5] Use `const` whenever you can [[P.10]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#p10-prefer-immutable-data-to-mutable-data) (but no const for member variables and return types [[C.12]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c12-dont-make-data-members-const-or-references-in-a-copyable-or-movable-type)).  [[meme]](https://github.com/janwilmans/guidelines/assets/5933444/e1f32720-76e9-41d2-a2cd-c7167a6fe881)
- [H.6] Use `[[nodiscard]]` for all `const` member functions returning a value.
- [H.7] Avoid returning values using arguments. [[F.20]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f20-for-out-output-values-prefer-return-values-to-output-parameters) [[F.21]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f21-to-return-multiple-out-values-prefer-returning-a-struct)
- [H.8] Use automatic resource management (RAII). [[P.8]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#p8-dont-leak-any-resources) [[C.31]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c31-all-resources-acquired-by-a-class-must-be-released-by-the-classs-destructor)
- [H.9] Follow the [rule of 0](https://en.cppreference.com/w/cpp/language/rule_of_three) or the rule of 5 in that order.
- [H.10] Avoid owning raw pointers. [[I.11]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#Ri-raw) [[F.26]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f26-use-a-unique_ptrt-to-transfer-ownership-where-a-pointer-is-needed) [[doc]](https://en.cppreference.com/w/cpp/memory)
- [H.11] Avoid manual memory management using `new`, `delete`, `malloc`, `free`, etc.
    - When working with Qt the use of the new keyword explicitly allowed.
- [H.12] Do not use [C-style casts](https://en.cppreference.com/w/cpp/language/explicit_cast). [[meme]](https://github.com/janwilmans/guidelines/assets/5933444/27784daa-1ed8-4d75-9482-0e3e2be1aae7)
- [H.13] Do not add member variables to classes used as interfaces. (Interfaces are defined as pure virtual classes that have a virtual = default destructor)
- [H.14] Do not use protected member variables.
- [H.15] Avoid the use of `volatile`, `const_cast`, `reinterpret_cast`, `typedef`, `register`, `extern`, `protected` or `va_arg`
- [H.16] Make all destructors of classes used in runtime polymorphism virtual. [[C.35]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c35-a-base-class-destructor-should-be-either-public-and-virtual-or-protected-and-non-virtual)
- [H.17] Avoid references as data members of a class

That's all folks! As you can see, C++ is easy ;) as long as you KISS.

## Oh wait, there is more!

Well there are [more details](guidelines_details.md) but I think if you follow the above guidelines, you are well on your way to writing good C++ code!
As you can see the above guidelines are quite bold, like: "do not use reinterpret_cast". 
And I can hear you think "but, sometimes...". 

EXACTLY: **sometimes** you need it.

And that is why (I repeat) these are guidelines, not rigid rules. 
If you can justify (to yourself): yes, I see the guidelines, but this case is special, then by all means, deviate. However, make it explicit. You may consider wrapping that `reinterpret_cast` you need into a function and let the function name explain the purpose or the reason **why** it is needed. 

## Feedback very welcome!

Finally, I encourage you to review and delve into the [details](guidelines_details.md). Please share your thoughts, whether you agree, disagree, or have suggestions for enhancements. This document is alive and will evolve over time. Let's make it better together!

You can find me as @janwilmans on Twitter/X or just create an issue on this repo and discuss.

I'm looking forward to hearing from all of you!

-- Jan
