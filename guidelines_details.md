# More detailed guidelines


## Files

-   Files should not exceed `500` lines. If exceeded: refactor.
    -  **Rationale**: Large files are an indication of poor decomposition and lack of decomposition makes the code harder to understand.
-   Use the `#pragma once` include guard in headers. 
    -  **Rationale**: It does the job of protecting the header from multiple inclusion and although non-standard! most compilers support it.
    -  Note: be aware the offers no protection from dubble inclusing if you sim-link a header. (don't do that ;)

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
    - **Rationale**: makes functions/interface harder to use correctly.
-   Pass fundamental types (e.g. `double`) and small object arguments by value
    - **Rationale**: passing things that fit in registers (64 bit -> 8 bytes) by referenfece is not a performance win and passing by value improves local reasoning.


<details><summary>Rationale</summary>Large files are an indication of poor decomposition and lack of decomposition makes the code hard to understand.</details>

## Classes

-   Use `explicit` on all constructors of non-trivial types that take at least one argument 
    - rationale: to prevent implicit conversions and make them visible at the call site
-   Put every class in its own file
-   Define interfaces as pure virtual classes that have a default virtual destructor and do not have member variables
-   Do not use aggregate initialization for defaults; either create constructors instead or use in-class initialization
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
-   Avoid globals. If really needed: use a static access method to encapsulate the global state (rationale: avoid the SIOF problem, see https://isocpp.org/wiki/faq/ctors#static-init-order)
-   Prefer `auto` in places where it makes code more readable
-   Use `auto *` when receiving a pointer
-   Prefer `static_cast` or `dynamic_cast` over C-style casts.
    -   Construction of explicit native types is allowed (i.e. `foo(double v) { int a(v);`)
-   Do not use raw pointers for ownership; use smart pointers instead. `std::unique_ptr` has negligible performance overhead compared to raw pointers.
-   Use `static constexpr` for scoped variables inside functions or classes
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
