# More detailed guidelines


## Files

-   Files should not exceed `500` lines. If exceeded: refactor.
-   Use the `#pragma once` include guard in headers

## Functions and lambdas

-   Return by value
    -   Wrap C-style functions with output parameters in a function that returns by value
-   Functions should not exceed `42` lines. If exceeded: refactor
-   Functions and lambdas should not have more than `3` arguments
-   Explicitly capture variables in lambdas
-   Do not use `inline` for performance reasons; compilers are smart enough to inline functions when possible
-   Do not return `nullptr` to indicate an error. Rationale: do not write code that can crash by not checking a return value.
-   Pass fundamental types (e.g. `double`) by value

## Classes

-   Use `explicit` on all constructors of non-trivial types that take at least one argument 
    - rationale: to prevent implicit conversions and make them visible at the call site
-   Put every class in its own file
-   Define interfaces as pure virtual classes that have a default virtual destructor and do not have member variables
-   Do not use aggregate initialization for defaults; either create constructors instead or use in-class initialization
-   Prefer initializer lists over assignment in constructor body
-   Make all destructors of classes in an inheritance hierarchy `virtual`
-   Declare `public`, `protected` and `private` in that order
-   Avoid `protected` (usually indicates a design problem)
-   Reference qualify functions that return references or reference types. https://godbolt.org/z/s6j8GPGfz
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
-   Prefer enum classes over booleans, rationale: it states the intent clearly at the callsite (https://godbolt.org/z/nPn1s6bP6)
-   Group related variables that are always passed together into structs (https://godbolt.org/z/nzE8crErj)

## Exceptions

-   Ensure exceptions thrown are handled on a higher level
-   Prefer exceptions on library boundaries for non-happy flow paths
-   Prefer standard exception types over custom exceptions
    -   Convert 3rd party exceptions to standard exception types from the stl library


## Misc

-   List class data members in order big to small (rationale: reduces padding)
-   Put switch statements in a separate function where each case only returns immediately (https://godbolt.org/z/c9vvod3GW)
-   Do not use more than `2` nested levels of conditional statements. If exceeded: refactor.
-   Do not use manual memory management (e.g. `new`, `delete`, `malloc`, `free`)
    -   Exception: when working with Qt `new` is explicitly allowed
-   Do not use `const_cast`, `reinterpret_cast`, `typedef`, `register` and `extern`
-   Prefer `nullptr` rather than `0` or `NULL`, rationale: `nullptr`` does not implicitly convert to non-pointers.
-   Prefer functionality from the STL library over third-party libraries (e.g. Boost)
-   Move `static_assert` out of headers, so they are checked only once.
-   Empty lines between data members is a code smell (rationale: if you wanted to group the data, you should add a struct)
-   Do not return reference types, such as `std::string_view` from a function.

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

## Examples

```
class Person
{
public:
    [[nodiscard]] std::string get_name() const;
    void set_name(std::string);
private:
    int m_name;
};
```


