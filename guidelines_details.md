# More detailed guidelines

I have ordered each section's guidelines in order of importance.

> **"I didn’t have time to write you a short letter, so I wrote you a long one.” -- Mark Twain**

<details><summary>Mottos</summary>

# Mottos

Motto's or Mantra's can help convey a certain idea or act as a reminder to do or not do things.
These are the mottos I find useful, some of them I learned from Gert-Jan de Vos who I'm eternally grateful for teaching me.

- KISS (Keep It Simple Stupid) followed by YAGNI (You Ain't Gonna Need It)
- DRY (Don't repeat yourself)
- Fail Early, use Guard Clauses (Check pre-conditions in a function and bail asap) [[P.7]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#p7-catch-run-time-errors-early)
- Design for debugging is a self-fulfilling prophesy
- More explicit code is better code
- Create a Pit of Success (Make interfaces and types hard to use incorrectly)
- Comments are a code smell
- Prefer the most fitting tool for the job

## KISS & YAGNI

**KISS** is perhaps the most difficult of all guidelines to describe and execute. Writing a simple (maybe elegant or short are also attributes) solution to a complicated problem or even not-writing a complicated solution to not-so difficult problem can sometimes be very hard.
Rabbit holes and pitfalls are omnipresent in practice and side-stepping them and keeping things simple is a true art.
As an example, I recently implemented a [ICMP PING function](https://github.com/janwilmans/explain_icmp_ping), to make an 'is the internet available' function,
but this cost me several hours to make and the result is 288 lines of code. One alternative could have been to run `system("ping -W1 -c1 8.8.8.8")` and check if the return is 0. Which of these solutions is appropriate depends entirely on the use case. Also the level of control you have over the process is wildly different. [[F.2]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f2-a-function-should-perform-a-single-logical-operation) [[F.3]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f3-keep-functions-short-and-simple) [[F.8]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f8-prefer-pure-functions)

**YAGNI** is about choosing wisely and timely. You could just use a system-based ping first, until it turns out that is not good enough and you really need more control over, let's say, the timeout. Only then you spend more time to implement a more complex solution. Programmers have a tendency to think they know what they or users are going to need in the future. YAGNI says: good, keep that in mind, but do not implement anything you do not need right now.

**DRY: DON'T REPEAT YOURSELF**: I realize the 'Don't repeat yourself' is ironic because I repeat some things in this document in multiple places, however writing documents is unlike writing code. The compiler reads all the code at once, but I do not expect the same from humans that read this document, some redundancy hopefully makes this document more understandable even if you just read one section. In software engineering the DRY principle refers to not repeating similar but functionally identical things in multiple places. Do not copy-paste code, just to modify it slightly, instead write re-usable code.

**FAIL EARLY**: Sometimes called 'Guard Clauses' or 'Return Early Pattern'. Detecting and handling errors as soon as they occur, but also exiting a function ASAP. This helps to reduce nested if-statement and improved readability.
A function should deal with pre-condition checks first. [example](https://cppcoach.godbolt.org/z/s6dY7sxY3) here to understand "what the function" does, the reader can focus on just lines 33-38. The pre-conditions or error-cases if there are any, are clearly separated from the happy-flow.

**COMMENTS ARE A CODE SMELL**: I do not mean: "All comments are bad". However, often comments are used to compensate in some way for the fact the author was aware there was something wrong or unclear but was not sure how to solve it. Comments like 'changing this enum breaks the world' are indicators of design problems. [examples](https://cppcoach.godbolt.org/z/Yz13K6TMc). Which by the way, doesn't mean you should remove them without changing the code.

If you are breaking any of the guidelines or are doing something unexpected, it is good separate that code into a function, let the name explain **what** it is doing and add a comment to explain **why**. [[I.30]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#i30-encapsulate-rule-violations)
Comments should explain WHY/HOW the code is doing something, try to cover the WHAT in the name of the class, function or lambda.

Here is an example of a comment that is a code smell. I think these comment is justified. However, I would not be happy to find this 'make_example' and I would refactor it to make sure we do not 'rely' on code with known UB. 

```
// this has undefined behavior according to UBSAN, but I tested it, it works fine.
void make_example();
```

I think the comments on 'isInternetAvailable()' are good, the name of function explains what we are trying to accomplish and comment explains how and why the author wrote it this way.

```
// this function ping's 8.8.8.8 because I don't know a better way to do an online check
// Returns true if the the ping-time is < 100ms.
bool isInternetAvailable();
```

**THE MOST FITTING TOOL**: With the most fitting tool I mean, if you have a choice between more generic solutions and more specific solutions the more specific solution is almost always less error-prone. An example of this is `std::lock_guard`, it cannot unlock early and it takes only one lockable object. While `std::scoped_lock` can lock multiple object at once and `std::unique_lock` can unlock early. If you do not require the latter features, prefer `std::lock_guard`, as it does not have extra features and thus is harder to use incorrectly.

Another example; `std::shared_ptr<T>` is very generic, objects of this type  can be copied and weak pointers can be created from it. In contrast `std::unique_ptr<T>` has a more specific use case, a limited set of features and is therefore less error-prone.

</details>

## Project structure

- Use advice from https://github.com/vector-of-bool/pitchfork to create a directory structure or invent your own, but use it consistently.

## Files

- Files should not exceed 2000 lines. If exceeded: refactor. 
  - **Rationale**: Large files are an indication of poor decomposition and lack of decomposition makes the code harder to understand.
- Put every class in its own file.
  - **Rationale**: helps to keep files smaller.
- Use the `#pragma once` include guard in headers.
  - **Rationale**: This is a bit contentious, because it is non-standard (but widely supported). It does the job of protecting the header from multiple inclusion and avoids the risk of having duplicate include-guard #defines. I think this out-weights the drawback of the fact that it offers no protection from multiple inclusion if you [sim-link](https://en.wikipedia.org/wiki/Pragma_once) files within a project. (don't do that ;)

## Rationales for the high level guidelines:

- [HL.1] Turn on warnings! and use sanitizers.
  - **Rationale**: Enabling compiler warnings helps catch potential issues early in the development process. It ensures that code is more robust and less prone to bugs by highlighting common pitfalls, such as unused variables, type mismatches, or potential logic errors. Using warnings as errors further enforces code quality by making sure these issues are addressed.
- [HL.2] Keep scope as limited as possible.
  - **Rationale**: Limiting the scope of variables enhances code clarity and reduces the likelihood of errors. It makes the code more modular and easier to understand by confining variables to the smallest possible context. This practice also helps prevent unintended interactions between different parts of the code.
- [HL.3] Initialize all variables at declaration. [[meme]](https://github.com/janwilmans/guidelines/assets/5933444/4592cf74-7957-46e8-8133-0d065bab56d8)
  - **Rationale**: Initializing variables at the point of declaration ensures that they have a known state, improving the stability and predictability of the code. It also makes the code easier to understand and reason about.
- [HL.4] Use `const` whenever you can [[P.10]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#p10-prefer-immutable-data-to-mutable-data) (but no const for member variables and return types [[C.12]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c12-dont-make-data-members-const-or-references-in-a-copyable-or-movable-type)). [[meme]](https://github.com/janwilmans/guidelines/assets/5933444/e1f32720-76e9-41d2-a2cd-c7167a6fe881)
  - **Rationale**: Using const for variables and member functions signifies that their value or behavior will not change, enhancing code safety and readability. `[[nodiscard]]` is used to indicate that the return value of a function should not be ignored, helping to prevent subtle bugs where return values are inadvertently discarded. However, adding const to member variables would make the class **uncopyable** and **unmoveable** because those operations require re-assignment that const does not allow.
- [HL.5] Use `[[nodiscard]]` for all `const` member functions returning a value
  - **Rationale**: const function can't modify state, so the only reason to call them is to get the return value
- [HL.6] Avoid returning values using arguments. [[F.20]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f20-for-out-output-values-prefer-return-values-to-output-parameters) [[F.21]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f21-to-return-multiple-out-values-prefer-returning-a-struct)
  - **Rationale**: breaks local reasoning for the caller, making reading and understanding the code harder.
- [HL.7] Use automatic resource management (RAII). [[P.8]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#p8-dont-leak-any-resources) [[C.31]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c31-all-resources-acquired-by-a-class-must-be-released-by-the-classs-destructor)
    - **Rationale**: Create a wrapper class for any resource that needs to be acquired and released. This prevents resource leaks, because on every exit-path, resources are automatically released by the RAII objects' destructor.
 - [HL.8] Follow the rule of 0 or the rule of 5 in that order.
   - **Rationale**: the [rule of 0 and 5](https://en.cppreference.com/w/cpp/language/rule_of_three) are explained well on [cppreference.com](https://en.cppreference.com/w/cpp/language/rule_of_three)
- [HL.9] No owning raw pointers. [[I.11]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#Ri-raw) [link](https://en.cppreference.com/w/cpp/memory) [[F.26]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f26-use-a-unique_ptrt-to-transfer-ownership-where-a-pointer-is-needed)
  - **Rationale**: Owning raw pointers are prone to memory leaks and dangling pointers. Using smart pointers (std::unique_ptr, std::shared_ptr) provides automatic memory management, reducing the risk of such errors. Add remark on non-owning raw pointers
- [HL.10] No manual memory management using `new`, `delete`, `malloc`, `free`, etc.
  - When working with Qt or [Copperspice](www.copperspice.com) the use of the new keyword explicitly allowed.
  - **Rationale**: Manual memory management is error-prone and can lead to memory leaks, double deletions, and other issues. Qt and Copperspice have its own memory management mechanism, so new for those types of classes is actually not indicative of manual memory management.
- [HL.11] Do not use C-style casts.
  - **Rationale**: C-style casts are less explicit and more prone to errors compared to C++-style casts (`static_cast`, `dynamic_cast`, `const_cast`, `reinterpret_cast`). C++-style casts provide better type safety and are easier to search for and review. They make the intention of the cast clearer, which improves code readability and maintainability.
- [HL.12] Do not add member variables to classes used as interfaces. (Interfaces are defined as pure virtual classes that have a virtual = default destructor)
  - **Rationale**: Interfaces (pure virtual classes) are meant to define a contract without imposing storage requirements. Adding member variables to such classes breaks this abstraction, leading to unnecessary overhead and potential misuse of the interface. Keeping interfaces pure enhances flexibility and decouples implementation details from the interface.
- [HL.13] Do not use protected member variables
  - **Rationale**: Protected member variables violate the encapsulation principle by exposing internal state to derived classes, which can lead to fragile and tightly coupled code. Instead, use protected methods to control access to the internal state, maintaining encapsulation while allowing controlled access when necessary.
- [HL.14] Avoid the use of `volatile`, `const_cast`, `reinterpret_cast`, `typedef`, `register`, `extern`, `protected` or `va_arg`
  - **Rationale**:
    - `volatile`: Generally misunderstood and misused, leading to potential issues with optimization and undefined behavior. Modern concurrency mechanisms provide safer alternatives.
    - `const_cast`: Violates const-correctness, which can lead to undefined behavior. Should only be used for calling legacy C functions.
    - `reinterpret_cast`: Potentially dangerous because it can cast any pointer type to any other pointer type, leading to type safety violations and undefined behavior.
    - `typedef`: Superseded by using, which is more powerful and consistent with other type alias features in C++11 and later.
    - `register`: Deprecated and ignored by modern compilers, making it obsolete.
    - `extern`: Encourages global state, which is generally bad practice as it can lead to code that is difficult to understand and maintain.
    - `protected`: Breaks encapsulation to some extend because it allows derived classes to access internal state.
    - `va_arg`: Passing to va_args assumes the correct type will be read. This is fragile because it cannot generally be enforced to be safe [[F.55]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f55-dont-use-va_arg-arguments)
- [HL.15] Make all destructors of classes used in runtime polymorphism virtual. [[C.35]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c35-a-base-class-destructor-should-be-either-public-and-virtual-or-protected-and-non-virtual)
  - **Rationale**: Ensuring destructors are virtual in base classes used polymorphically prevents undefined behavior when derived class objects are deleted through base class pointers. This ensures that the correct destructor is called, which is crucial for proper resource cleanup in derived classes.
- [HL.16] Avoid references as data members of a class
  - **Rationale**: the reason the same as for 'avoid adding const to member variables', since re-assignment is not possible, the class would become **uncopyable** and **unmoveable** 

## Functions and lambdas

- add `[[nodiscard]]` and `const` to all getters
  - **Rationale**: both enable the compiler to give you proper error messages, it makes the function hard to use incorrectly.
- Return by value.
  - Wrap C-style functions with output parameters in a function that returns by value.
  - **Rationale**: helps with local reasoning about object lifetime, reduces lifetime dependencies.
- Keep functions limited to 42 lines. If a function exceeds this limit, refactor it while balancing the need for concise code and keeping related logic together.
  - **Rationale**: helps with (local) reasoning about the code, encourages decomposition
- Functions and lambdas should not have more than `3` arguments. [[I.23]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#i23-keep-the-number-of-function-arguments-low)
  - **Rationale**: this arbitrary limit encourages the author to consider if the function could/should not be split into multiple functions.
- Explicitly capture variables in lambdas.
  - **Rationale**: explicit capturing expresses intent, avoids capturing 'this' by accident.
- Do not use `inline` for performance reasons.
  - **Rationale**: compilers are smart enough to inline functions when possible.
- Do not return `nullptr` to indicate an error.
  - **Rationale**: makes functions/interface harder to use correctly, each caller must check for `nullptr`.
- Pass fundamental types (e.g. `double`) and small object arguments by value.
  - **Rationale**: passing things that fit in registers (64 bit -> 8 bytes) by reference is not a performance win and passing by value improves local reasoning.

## Classes

- Add `[[nodiscard]]` to `const` member functions in the header, it adds nothing to repeat `[[nodiscard]]` for member functions in the implementation.
  - **Rationale**: both enable the compiler to give you proper error messages, it makes the function hard to use incorrectly.
- Use `explicit` on all constructors of non-trivial types that take at least one argument.
  - **Rationale**: to prevent implicit conversions and make the construction visible at the call site.
- Define interfaces as pure virtual classes that have a default virtual destructor and do not have member variables.
  - **Rationale**: Interfaces are a mechanism to separate the interface from the implementation, adding members would contradict this purpose[^1].
- Use in-class initialization or in initializer lists to initialize all members [[C.41]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c41-a-constructor-should-create-a-fully-initialized-object) [[C.43]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c43-ensure-that-a-copyable-class-has-a-default-constructor) [[C.45]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c45-dont-define-a-default-constructor-that-only-initializes-data-members-use-default-member-initializers-instead)
  - **Rationale**: when full initialization is part of the type, it makes it harder to use incorrectly. 
- Mark all destructors of classes in an inheritance hierarchy `virtual` or `override`. [[C.35]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c35-a-base-class-destructor-should-be-either-public-and-virtual-or-protected-and-non-virtual)
  - **Rationale**: this prevents [subtle destruction bugs](https://cppcoach.godbolt.org/z/ebEPhzfbs).
- Declare `public`, `protected` and `private` in that order.
  - **Rationale**: consistent style improves readability
- Avoid `protected`, it usually indicates a design problem .
  - **Rationale**: protected breaks the separation of concerns the base provides and makes the hierarchy harder to reason about
- Make RAII class undiscardable using `[[nodiscard]]`
  - **Rationale**: this make them [harder to use incorrectly](https://cppcoach.godbolt.org/z/1d5Pq9nYn).
- Avoid assigning members in the constructor body, use the member initializer list or in-class initialization.
  - **Rationale**: makes the default constructor mandatory and forces [double initialization](https://cppcoach.godbolt.org/z/87eWx351f)
- mark all constructors with a single argument `explicit` [[C.46]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c46-by-default-declare-single-argument-constructors-explicit) [[C.48]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c48-prefer-default-member-initializers-to-member-initializers-in-constructors-for-constant-initializers) [[C.49]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c49-prefer-initialization-to-assignment-in-constructors)

## Variables

- Keep scope of variables as small as possible
  - **Rationale**: a smaller scope makes local reasoning easier and reduces opportunities to make mistakes
- Initialize variables at declaration
  **Rationale**:  Initializing variables at the point of declaration ensures that they have a known state, improving the stability and predictability of the code. It also makes the code easier to understand and reason about.- Declare variables on individual lines (i.e. no `int a, b, c;`)
  - **Rationale**: this improved readability and goes hand-in-hand with: initialize all variables at declaration.
- Use `const` whenever possible
  - **Rationale**: Using const indicates that a variable's value will not change after initialization, which helps prevent accidental modifications. It makes the code safer and more predictable.
- Do not use const for member variables
  - **Rationale**: Const member variables make the type unmovable and unassignable, which can complicate the usage of objects, especially when using standard library containers or algorithms that rely on move semantics.
- Do not use references as member variables
  - **Rationale**: Reference members also make the type unmovable and unassignable, limiting the flexibility and usability of the class.
- Avoid globals. If really needed: use a static access method to encapsulate the global state
  - **Rationale**: Global variables make debugging difficult due to their wide scope.
  - **Note** avoid the SIOF problem, see https://isocpp.org/wiki/faq/ctors#static-init-order
- Prefer `auto` in places where it makes code more readable
  - **Rationale**: do not repeat yourself, auto variables cannot be left uninitialized. That being said, don't overuse it.
- Use `auto *` when receiving a pointer.
  - **Rationale**: plain 'auto' would hide the fact that you are receiving a pointer.
- Prefer `static_cast` or `dynamic_cast` over C-style casts.
  - Explicitly constructing native types to do narrowing conversion is allowed (i.e. `void example(double v) { int a(v);`)
  - **Rationale**: C++ style casting make the type conversion explicit, which helps understanding the programmer's intent
- Do not use raw pointers for ownership.
  - **Rationale**: Use RAII objects or smart pointers instead. `std::unique_ptr` has minimal performance overhead and ensures that an object has a single owner.
- Use `static constexpr` for scoped variables inside implementation files, functions or classes.
  - **Rationale**: Static constexpr ensures that the variable is constant and has internal linkage, meaning it is limited to the scope in which it is declared.
- Use `inline constexpr` for globally scoped constants.
  - **Rationale**: Allows the definition of a constant in a header file without violating the one definition rule (ODR), ensuring that the constant has internal linkage but can still be included in multiple translation units.
- Prefer enum classes over booleans
  - **Rationale**: It states the intent clearly at the call site. (https://cppcoach.godbolt.org/z/7eP7vE9G5)
- Group related variables that are always passed together into structs. (https://cppcoach.godbolt.org/z/vxnxK1sv7)
  - **Rationale**: Grouping related variables enhances code organization and readability

## Exceptions

- Ensure exceptions thrown are handled on a higher level
  - **Rationale**: Unhandled (uncaught) exceptions cause undefined behavior in C++, typically this will crash the program.
- Prefer standard exception types over custom exceptions
  - Convert 3rd party exceptions that do not inherit from std::exception to exception that do.
  - **Rationale**: There is no way for (higher level) functions to know what 3rd party exceptions to catch and if they are thrown and unhandled, the program will crash.

## Misc

- List class data members in order big to small
  - **Rationale**: Arranging data members from largest to smallest size can reduce memory padding aka alignment overhead. 
- Put switch statements in a separate function where each case only returns immediately. [example](https://cppcoach.godbolt.org/z/hca48Gjnq)
  - **Rationale**: Isolating switch statements into separate functions and having each case return immediately simplifies the logic, making the code more readable and maintainable. 
- Do not use more than `2` nested levels of conditional statements. If exceeded: refactor. [[F.56]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#f56-avoid-unnecessary-condition-nesting)
  - **Rationale**: By limiting nesting to two levels, the code remains more straightforward and easier to follow. If more nesting is needed, it typically indicates that the function is doing too much and should be refactored into smaller, more manageable pieces.
- Prefer `nullptr` over `0` or `NULL`.
  - **Rationale**: `nullptr`` does not implicitly convert to non-pointers
- Prefer functionality from the STL library over third-party libraries (e.g. Boost)
  - **Rationale**: Using the STL reduces dependencies on third-party libraries, simplifies builds, reduces potential for compatibility issues, and ensures long-term code stability.
- Move `static_assert` out of headers, so they are checked only once.
  - **Rationale**: Placing static_assert in implementation files rather than headers ensures that assertions are checked only once, reducing compile times and avoiding redundant checks across multiple translation units. 
- Empty lines between data members is a code smell.
  - **Rationale**: indicative of poorly thought-out class design where related members are not grouped together. 
- Do not return reference types, such as pointers or `std::string_view` from a function.
  - **Rationale**: this breaks local reasoning and because the called not need to know about the lifetime of the referenced object.
- Use `std::string_view` for arguments to a function that do not care about zero-termination of the passed string.
  - **Rationale**: it is efficient and non-owning, just a pointer and a size, it avoids unneeded copies and also allows you to pass both `std::string` and `const char*`.
- Use `const std::string&` for arguments to function that **DO** care about zero-termination.
  - **Rationale**: specifically, functions that wrap C-functions should do this (and **NOT** use std::string_view, because it would require internal code to guarantee the zero-termination).
- Use `std::string` and pass strings by value on constructor arguments that sink their value (arguments that are moved into a member
  variable).
  - **Rationale**: this allows the caller to choose to move into the argument or make only one copy.
- Use `const std::filesystem::path& path` for strings that refer to file-like objects, such as devices or files.
  - **Rationale**: filesystem::path expresses intent, its `.c_str()` guarantees zero-termination and `path` comes with handy functions.
- do not use exceptions for code-flow decisions aka. do not throw if you expect you will need to catch the exception as part of the
  business logic of your application. Exceptions are for 'exceptional' cases, so only when continuing normal execution isn't possible.
  - **Rationale**: using exceptions for business logic breaks the concept of local reasoning, you 'expect' certain callees to throw under certain conditions. Also stack-unwinding it not free in terms of performance; using exceptions for code-flow decisions leads to less readable, harder to maintain, and potentially less performant code
- Avoid `requires requires`
  - **Rationale**: it is a shorthand for an in-place unnamed concept, give it a name.


<details><summary>Discussion</summary>

## Discussion

These discussion points lack proper guidance, if you have suggestions, please create an issue.

- When is `std::optional` a good solution? A function that may or may not produce output? required checking the result on every call seems bad.
  - **Rationale**: It explicitly communicates that the absence of a value is a valid state state should be checked against.

- Prefer exceptions on library boundaries for non-happy flow paths, not sure about this, exceptions should't be used to control code flow.
- When to use assertions and when to use exceptions? [[I.10]](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#i10-use-exceptions-to-signal-a-failure-to-perform-a-required-task)
- use `noexcept` on function you know cannot throw an exception for performance, discuss how to deal with changing code over time.
- use `constexpr` + `noexcept` on getters?
- start by making every function constexpr and only take it away only when needed?
- Delete reference qualified overloads of getters that return references or reference types. experimental: 
  - https://cppcoach.godbolt.org/z/eszM1esas

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
```

```
// applies: filesystem, optional

// the comments are to illustrate the example, 
// in production code I would not recommend to add these comments, because the 
// reverse is true: you used 'std::filesystem::path' so you are expressing intent 
// this function takes a 'file-like object'.

#include <filesystem>
#include <optional>

#include <sys/stat.h>

// referring to a file-like object so use filesystem::path
std::optional<int> get_filesize(const std::filesystem::path& path)
{
    struct stat64 meta_data = {};
    if (0 == stat64(path.c_str(), &meta_data)) // stat64 requires zero-termination
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

# work in progress:

The C++ core guideline references have been added up to:

* https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#ccon-containers-and-other-resource-handles