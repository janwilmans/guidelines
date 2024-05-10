# More detailed guidelines

I try to order each section's guidelines order of importance.

> **"I didn’t have time to write you a short letter, so I wrote you a long one.” -- Mark Twain**

Simplifications; some of the guidelines or rationales say 'Just don't do that', in these cases I do mean to be cheeky. I mean
try to avoid or remove those cases if you have them, because I could not come up with a good guideline to deal with those situations.
If you have ideas for improvement in this regard, feel free to leave me a note.

<details><summary>Mottos</summary>

# Mottos 

Motto's or Mantra's can help convey a certain idea or act as a reminder to do or not do things.
These are the mottos I find useful, some of them I learned from Gert-Jan de Vos who I'm eternally grateful for teaching me.

- KISS (Keep It Simple Stupid) followed by YAGNI (You Ain't Gonna Need It)
- DRY (Don't repeat yourself)
- Fail Early. (Check pre-conditions in a function and bail asap)
- Design for debugging is a self-fulfilling prophesy
- More explicit code is better code
- Create a Pit of Success (Make interfaces and types hard to use incorrectly)
- Comments are a code smell
- Prefer the most fitting tool for the job

## KISS & YAGNI

**KISS** is perhaps the most difficult of all guidelines to describe and execute. Writing a simple (maybe elegant or short are also attributes) solution to a complicated problem or even not-writing a complicated solution to not-so difficult problem can sometimes be very hard.
Rabbit holes and pitfalls are omnipresent in practice and side-stepping them and keeping things simple is a true art.
As an example, I recently implemented a [ICMP PING function](https://github.com/janwilmans/explain_icmp_ping), to make an 'is the internet available' function, 
but this cost me several hours to make and the result is 288 lines of code. One alternative could have been to run `system("ping -W1 -c1 8.8.8.8")` and check if the return is 0. Which of these solutions is appropriate depends entirely on the use case. Also the level of control you have over the process is wildly different.

**YAGNI** is about choosing wisely and timely. You could just use a system-based ping first, until it turns out that is not good enough and you really need more control over, let's say, the timeout. Only then you spend more time to implement a more complex solution. Programmers have a tendency to think they know what they or users are going to need in the future. YAGNI says: good, keep that in mind, but do not implement anything you do not need right now.

**DRY: DON'T REPEAT YOURSELF**: I realize the 'Don't repeat yourself' is ironic because I repeat some things in this document in multiple places, however writing documents is unlike writing code. The compiler reads all the code at once, but I do not expect the same from humans that read this document, some redundancy hopefully makes this document more understandable even if you just read one section. In software engineering the DRY principle refers to not repeating similar but functionally identical things in multiple places. Do not copy-paste code, just to modify it slightly, instead write re-usable code.

**FAIL EARLY**: Detecting and handling errors as soon as they occur, but also exiting a function ASAP. This helps to reduce nested if-statement and improved readability.
A function should deal with per-condition checks first. [example](https://cppcoach.godbolt.org/z/s6dY7sxY3) here to understand "what the function" does, the reading can focus on just lines 33-38. The pre-conditions or error-cases if there are any, are clearly separated from the happy-flow.

**COMMENTS ARE A CODE SMELL**: I do not mean: "All comments are bad". Often comments are used to compensate in some way for the fact the author was aware there was something wrong or unclear but was not sure how to solve it. Comments like 'changing this enum breaks the world' are indicators of design problems. [examples](https://cppcoach.godbolt.org/z/Yz13K6TMc)

If you are breaking any of the guidelines or are doing something unexpected, if it fine to add a comment.
Comments should explain WHY/HOW the code is doing something, try to cover the WHAT in the name of the class, function or lambda.

These are examples of comments that are code smells, which doesn't mean I think you should remove them without changing the code ;)
I think these comments are justified. I would not be happy to find the first 'make_example' and I would try to refactor to make sure we do not 'rely' on code with known UB. However, I think the 'isInternetAvailable()' is fine, the name of function explains what we are trying to accomplish and comment explains how and why the author wrote it this way.

```
// this has undefined behavior according to UBSAN, but I tested it, it works fine.
void make_example();
```

```
// this function ping's 8.8.8.8 because I don't know a better way to do an online check
// Returns true if the the ping-time is < 100ms.
bool isInternetAvailable();
```

**THE MOST FITTING TOOL**: With the most fitting tool I mean, if you have a choise between more generic solutions and more specific solutions the more specific solution is almost always less error-prone. An example of this is `std::lock_guard`, it cannot unlock early and it takes only one lockable object. While `std::scoped_lock` can lock multiple object at once and `std::unique_lock` can unlock early. If you do not require the latter features, prefer `std::lock_guard`, as it does not have extra features and thus is harder to use incorrectly.

</details>

## Project structure

-   Use advice from https://github.com/vector-of-bool/pitchfork to create a directory structure or invent your own, but use it consistently.

## Files

-   Files should not exceed `500` lines. If exceeded: refactor.
    -  **Rationale**: Large files are an indication of poor decomposition and lack of decomposition makes the code harder to understand.
-   Put every class in its own file.
    - **Rationale**: helps to keep files smaller.
-   Use the `#pragma once` include guard in headers.
    -  **Rationale**: It does the job of protecting the header from multiple inclusion and although non-standard! it is widely supported.
    -  Note: be aware the offers no protection from multiple inclusion if you [sim-link](https://en.wikipedia.org/wiki/Pragma_once) files. (don't do that ;) 

## Functions and lambdas

- add `[[nodiscard]]` and `const` to all getters 
    - **Rationale**: both enable the compiler to give you proper error messages, it makes the function hard to use incorrectly.
-   Return by value.
    -   Wrap C-style functions with output parameters in a function that returns by value.
    - **Rationale**: helps with local reasoning about object lifetime, reduces lifetime dependencies.
-   Functions should not exceed `42` lines. If exceeded: refactor.
    - **Rationale**: helps with (local) reasoning about the code, encourages decomposition.
-   Functions and lambdas should not have more than `3` arguments.
    - **Rationale**: this arbitrary limit encourages the author to consider if the function could/should not be split into multiple functions.
-   Explicitly capture variables in lambdas.
    - **Rationale**: explicit capturing expresses intent, avoids capturing 'this' by accident.
-   Do not use `inline` for performance reasons.
    - **Rationale**: compilers are smart enough to inline functions when possible.
-   Do not return `nullptr` to indicate an error.
    - **Rationale**: makes functions/interface harder to use correctly, each caller must check for `nullptr`.
-   Pass fundamental types (e.g. `double`) and small object arguments by value.
    - **Rationale**: passing things that fit in registers (64 bit -> 8 bytes) by reference is not a performance win and passing by value improves local reasoning.

## Classes

-   Add `[[nodiscard]]` to `const` member functions in the header, it adds nothing to repeat `[[nodiscard]]` for member functions in the implementation.
    - **Rationale**: both enable the compiler to give you proper error messages, it makes the function hard to use incorrectly.
-   Use `explicit` on all constructors of non-trivial types that take at least one argument.
    - **Rationale**: to prevent implicit conversions and make the construction visible at the call site.
-   Define interfaces as pure virtual classes that have a default virtual destructor and do not have member variables.
    - **Rationale**: Interfaces are a mechanism to separate the interface from the implementation, adding members would contradict this purpose[^1].
-   Do not rely on aggregate initialization for assigning default value to members, either create constructors instead or use in-class initialization.
    - **Rationale**: when initialization is part of the type, it makes it harder to use incorrectly.
-   Prefer member initializer lists over assignment in constructor body.
    - **Rationale**: it's more efficient, it is construction instead construction followed by assignment.
-   Mark all destructors of classes in an inheritance hierarchy `virtual` or `override`.
    - **Rationale**: this prevents [subtle destruction bugs](https://cppcoach.godbolt.org/z/ebEPhzfbs).
-   Declare `public`, `protected` and `private` in that order.
    - **Rationale**: consistent style improves readability 
-   Avoid `protected`, it usually indicates a design problem .
    - **Rationale**: protected breaks the separation of concerns the base provides and make the hierarchy harder to reason about
-   Add `[[nodiscard]]` to RAII classes
    - **Rationale**: make them [hard to use incorrectly](https://cppcoach.godbolt.org/z/1d5Pq9nYn).

## Variables

-   Keep scope of variables as small as possible
    **Rationale**: a smaller scope makes local reasoning easier and reduces opportunities to make mistakes
-   Declare variables on individual lines (i.e. no `int a, b, c;`)
    **Rationale**: this improved readability and goes hand-in-hand with, initialize all variables at declaration.
-   Use `const` whenever possible
    -   Do not use const for member variables (rationale: const member variables make the type unmovable and unassignable)
-   Do not use references as member variables (rationale: reference members make the type unmovable and unassignable)
-   Initialize variables at declaration
    **Rationale**: uninitialized values give unpredictable behavior and 
-   Avoid globals. If really needed: use a static access method to encapsulate the global state
    - **Rationale**: avoid the SIOF problem, see https://isocpp.org/wiki/faq/ctors#static-init-order
-   Prefer `auto` in places where it makes code more readable
    - **Rationale**: do not repeat yourself, auto variables cannot be left uninitialized. That being said, don't overuse it. example

-   Use `auto *` when receiving a pointer.
    - **Rationale**: plain 'auto' would hide the fact that you are receiving a pointer.
-   Prefer `static_cast` or `dynamic_cast` over C-style casts.
    - Construction of explicit native types is allowed (i.e. `foo(double v) { int a(v);`)
    - **Rationale**:
-   Do not use raw pointers for ownership; use smart pointers instead. `std::unique_ptr` has negligible performance overhead compared to raw pointers.
-   Use `static constexpr` for scoped variables inside implementation files, functions or classes.
-   Use `inline constexpr` for globally scoped constants.
-   Prefer enum classes over booleans, rationale: it states the intent clearly at the call site. (https://cppcoach.godbolt.org/z/7eP7vE9G5)
-   Group related variables that are always passed together into structs. (https://cppcoach.godbolt.org/z/vxnxK1sv7)

## Exceptions

-   Ensure exceptions thrown are handled on a higher level
-   Prefer exceptions on library boundaries for non-happy flow paths
-   Prefer standard exception types over custom exceptions
    -   Convert 3rd party exceptions to standard exception types from the stl library


## Misc

-   List class data members in order big to small (rationale: reduces padding)
-   Put switch statements in a separate function where each case only returns immediately. [example](https://cppcoach.godbolt.org/z/hca48Gjnq)
-   Do not use more than `2` nested levels of conditional statements. If exceeded: refactor.
-   Do not use manual memory management (e.g. `new`, `delete`, `malloc`, `free`)
    -   Exception: when working with Qt `new` is explicitly allowed
-   Do not use `const_cast`, `reinterpret_cast`, `typedef`, `register` and `extern`
-   Prefer `nullptr` rather than `0` or `NULL`, rationale: `nullptr`` does not implicitly convert to non-pointers.
-   Prefer functionality from the STL library over third-party libraries (e.g. Boost)
-   Move `static_assert` out of headers, so they are checked only once.
-   Empty lines between data members is a code smell.
    - **Rationale**: if you wanted to group the data, you should probably add a struct.
-   Do not return reference types, such as pointers or `std::string_view` from a function.
    - **Rationale**: this breaks local reasoning and it is very easy to create lifetime issues.
-   Use `std::string_view` for arguments to a function that do not care about zero-termination of the passed string.
    - **Rationale**: it is efficient and non-owning, just a pointer and a size, it avoids unneeded copies and also allows you to pass both `std::string` and `const char*`.
-   Use `const std::string&` for arguments to function that **DO** care about zero-termination.
    - **Rationale**: specifically, functions that wrap C-functions should do this (and **NOT** use std::string_view, because it would require internal code to guarantee the zero-termination).
-   Use `std::string` and pass strings by value on constructor arguments that sink their value (arguments that are moved into a member variable).
    - **Rationale**: this allows the caller to choose to move into the argument or make only one copy.
-   Use `const std::filesystem::path& path` for strings that refer to file-like objects, such as devices or files.
    - **Rationale**: filesystem::path expresses intent, its `.c_str()` guarantees zero-termination and `path` comes with handy functions.


<details><summary>Discussion</summary>

## Discussion

These discussion points lack proper guidance, if you have suggestions, please create an issue. 

-   Fail Early. Check conditions in a function and bail out asap if possible.
-   When is `std::optional` a good solution? A function that may or may not produce output? required checking the result on every call seems bad.
-   When to use assertions and when to use exceptions?
-   Avoid `requires requires`, rationale: it is a shorthand for an in-place defined unnamed concept, give it a name.
-   use `noexcept` on function you know cannot throw an exception for performance, discuss how to deal with changing code over time.
-   use `constexpr` + `noexcept` on getters?
-   start by making every function constexpr and only take it away only when needed?
-   do not use exceptions for code-flow decisions
-   Reference qualify functions that return references or reference types. experimental: https://godbolt.org/z/s6j8GPGfz
    https://cppcoach.godbolt.org/z/dozvvsEv8
    https://cppcoach.godbolt.org/z/cG3sTTdd5

    
</details>

## Examples

What does code that applies these guidelines look like?

```cpp
#include <string>

// applies: explicit, sink, nodiscard, const
class Example
{
public:
    explicit Example(std::string name) : m_name(std::move(name))
    {
    }

    [[nodiscard]] std::string get_name() const
    {
        return m_name;
    }

    void set_name(std::string name)
    {
        m_name = std::move(name);
    }

private:
    std::string m_name;
};

#include <filesystem>
#include <optional>

#include <sys/stat.h>

// zero-termination, filesystem::path
std::optional<int> get_filesize(const std::filesystem::path& path)
{
    struct stat64 meta_data = {};
    if (0 == stat64(path.c_str(), &meta_data))  //  stat64 requires zero-termination
    {
        return meta_data.st_size;
    }
    return {}; // return no value if the size could not be read
}
```

# counter examples:

Here is an example that violates many guidelines, but I think rightfully so:

ignored guidelines:

- returning reference types from member functions (pointers).
- storing reference types (std::string_view).
- function taking 4 arguments.

It's not super shocking but I wanted to showcase that code can still fine even if it is ignoring some of the guidelines.
I put the 'offending' code in a `details` namespace, so it is clear that part is not the 'user API' for logging, only the two `log_debug` and `log_error` are.

[log_debug() code](https://cppcoach.godbolt.org/z/7za8ccPxx) 


[^1]: Violation of Interface Segregation Principle (ISP): The Interface Segregation Principle states that a class should not be forced to implement interfaces it does not use. If an interface contains member variables that are not relevant to a particular class, it violates ISP
