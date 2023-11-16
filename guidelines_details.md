# More detailed guidelines

<details><summary>Mottos</summary>

# Motto's 

Motto's or Mantra's can help convey a certain idea or act as a reminder to do or not do things.
These are the mottos I find useful, some of them I learned from Gert-Jan de Vos who I'm eternally grateful for teaching me.

- KISS (Keep It Simple Stupid)
- Don't repeat yourself
- Design for debugging is a self-fulfilling prophesy 
- More explicit code is better code
- Create a Pit of Success (Make interfaces and types hard to use incorrectly)
- Comments are a code smell

Simplifications; some of the guidelines or rationales say 'Just don't do that', in these cases I do mean to be cheeky. I mean
try to avoid or remove those cases if you have them, because I could not come up with a good guideline to deal with those situations.
If you have ideas for improvement in this regard, feel free to leave me a note.

I realize the 'Don't repeat yourself' is ironic because I repeat some things in this document in multiple places, however writing documents
is unlike writing code. The compiler reads all the code at once, but I do not expect the same from humans that read this document.

About Comments: I do mean, ALL comments are a code smell, but often comments are used to compensate in some way to the fact the author was aware there was something wrong but was not sure how to solve it. For example:

```
// this has undefined behavior, but I tested it, it works fine.
void example() {}
```



</details>

## Project structure

-   Use advice from https://github.com/vector-of-bool/pitchfork to create a directory structure or invent your own, but use it consistently.

## Files

-   Files should not exceed `500` lines. If exceeded: refactor.
    -  **Rationale**: Large files are an indication of poor decomposition and lack of decomposition makes the code harder to understand.
-   Put every class in its own file
    - **Rationale**: helps to keep files smaller
-   Use the `#pragma once` include guard in headers. 
    -  **Rationale**: It does the job of protecting the header from multiple inclusion and although non-standard! it is widely supported.
    -  Note: be aware the offers no protection from multiple inclusion if you [sim-link](https://en.wikipedia.org/wiki/Pragma_once) files. (don't do that ;) 

## Functions and lambdas

-   Return by value
    -   Wrap C-style functions with output parameters in a function that returns by value
    -   **Rationale**: helps with local reasoning about object lifetime, reduces lifetime dependencies.
-   Functions should not exceed `42` lines. If exceeded: refactor
    -   **Rationale**: helps with (local) reasoning about the code, encourages decomposition.
-   Functions and lambdas should not have more than `3` arguments
    -   **Rationale**: this arbitrary limit encourages the author to consider if the function could/should not be split into multiple functions.
-   Explicitly capture variables in lambdas
    - **Rationale**: explicit capturing expresses intent, avoids capturing 'this' by accident.
-   Do not use `inline` for performance reasons
    - **Rationale**: compilers are smart enough to inline functions when possible
-   Do not return `nullptr` to indicate an error.
    - **Rationale**: makes functions/interface harder to use correctly, each caller must check for `nullptr`.
-   Pass fundamental types (e.g. `double`) and small object arguments by value
    - **Rationale**: passing things that fit in registers (64 bit -> 8 bytes) by referenfece is not a performance win and passing by value improves local reasoning.

## Classes

-   Use `explicit` on all constructors of non-trivial types that take at least one argument 
    - **Rationale**: to prevent implicit conversions and make the construction visible at the call site
-   Define interfaces as pure virtual classes that have a default virtual destructor and do not have member variables
    - **Rationale**: Interfaces are a mechanism to separate the interface from the implementation, adding members would contradict this purpose[^1]
-   Do not rely on aggregate initialization for assigning default value to members, either create constructors instead or use in-class initialization
    - **Rationale**: when initialization is part of the type, it makes it harder to use incorrectly
-   Prefer initializer lists over assignment in constructor body
-   Mark all destructors of classes in an inheritance hierarchy `virtual` or `override` (https://godbolt.org/z/7E7Yx6faz)
-   Declare `public`, `protected` and `private` in that order
-   Avoid `protected` (usually indicates a design problem)
-   Add `[[nodiscard]]` to `const` member functions in header, do not repeat `[[nodiscard]]` for member functions in the implementation
-   Add `[[nodiscard]]` to RAII classes to make them harder to use incorrectly
-   Make getter member functions `const`


## Variables

-   Keep scope of variables as small as possible
-   Declare variables on individual lines (i.e. no `int a, b, c;`)
-   Use `const` whenever possible
    -   Do not use const for member variables (rationale: const member variables make the type unmovable and unassignable)
-   Do not use references as member variables (rationale: reference members make the type unmovable and unassignable)
-   Initialize variables at declaration
-   Avoid globals. If really needed: use a static access method to encapsulate the global state
    - **Rationale**: avoid the SIOF problem, see https://isocpp.org/wiki/faq/ctors#static-init-order
-   Prefer `auto` in places where it makes code more readable
    - **Rationale**: do not repeat yourself
-   Use `auto *` when receiving a pointer
    - **Rationale**: plain 'auto' would hide the fact that you are receiving a pointer.
-   Prefer `static_cast` or `dynamic_cast` over C-style casts.
    -   Construction of explicit native types is allowed (i.e. `foo(double v) { int a(v);`)
-   Do not use raw pointers for ownership; use smart pointers instead. `std::unique_ptr` has negligible performance overhead compared to raw pointers.
-   Use `static constexpr` for scoped variables inside implementation files, functions or classes
-   Use `inline constexpr` for globally scoped constants
-   Prefer enum classes over booleans, rationale: it states the intent clearly at the call site (https://godbolt.org/z/7Mfo6oz1Y)
-   Group related variables that are always passed together into structs (https://godbolt.org/z/7T3hqYnT4)

## Exceptions

-   Ensure exceptions thrown are handled on a higher level
-   Prefer exceptions on library boundaries for non-happy flow paths
-   Prefer standard exception types over custom exceptions
    -   Convert 3rd party exceptions to standard exception types from the stl library


## Misc

-   List class data members in order big to small (rationale: reduces padding)
-   Put switch statements in a separate function where each case only returns immediately (https://godbolt.org/z/cf8sjr8r3)
-   Do not use more than `2` nested levels of conditional statements. If exceeded: refactor.
-   Do not use manual memory management (e.g. `new`, `delete`, `malloc`, `free`)
    -   Exception: when working with Qt `new` is explicitly allowed
-   Do not use `const_cast`, `reinterpret_cast`, `typedef`, `register` and `extern`
-   Prefer `nullptr` rather than `0` or `NULL`, rationale: `nullptr`` does not implicitly convert to non-pointers.
-   Prefer functionality from the STL library over third-party libraries (e.g. Boost)
-   Move `static_assert` out of headers, so they are checked only once.
-   Empty lines between data members is a code smell (rationale: if you wanted to group the data, you should add a struct)
-   Do not return reference types, such as `std::string_view` from a function.
-   Use `std::string_view` for arguments to function that do not care about zero-termination of the passed string
-   Use `const std::string&` for arguments to function that DO care about zero-termination
-   Use `std::string` and pass strings by value on constructor arguments that sink (move-construct a member variable) their value 
    - rationale: this allow the caller choose to move into the argument, or make only one copy.
-   Use `const std::filesystem::path& path` for strings that refer to file-like objects, such as devices or files.
    - rationale: filesystem::path expresses intent, .c_str() gives zero-termination and `path` comes with handy functions.


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

[^1]: Violation of Interface Segregation Principle (ISP): The Interface Segregation Principle states that a class should not be forced to implement interfaces it does not use. If an interface contains member variables that are not relevant to a particular class, it violates ISP