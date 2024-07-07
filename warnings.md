# Setting compiler warnings

I have standard 'sets' of warnings I use for new projects, these are them.

## GCC

```
-Werror -Wconversion -Wsign-conversion -Wall -Wextra -Wpedantic -Wdeprecated -fsanitize=address,undefined 
```

## Clang

```
-Werror -Wconversion -Wsign-conversion -Wall -Wextra -Wpedantic -Wdeprecated -fsanitize=address,undefined 
```

## MSVC

Just having /W3 is is really good start, but /W4 is of course even better, certainly for new projects, see below for a more complete set.

```
/W4 
```

I also use the defines: `NOMINMAX` and `WIN32_LEAN_AND_MEAN` for a little more standard compliance and less old headers included by default if you're going to include 'windows.h'.

# More elaborate sets

There are also a couple of extra nice warnings you could add, I provide them here in CMAke syntax. I generally put this in a separate file 'CMakeLists.warnings.cmake', also include it for projects that I want to enforce these warnings on.
However, I think the recommended CMake approach is actually to make a 'target' with these options 'public' and make your targets that want these options depend on it. So do want to will with that ;)
You could look at the [DebugView++](https://github.com/CobaltFusion/DebugViewPP/blob/develop/CMakeLists.txt#L55) CMake setup as an example

## GCC and CLANG

```
add_compile_options(
  -Wall
  -Wextra
  -pedantic
  -Wcast-align
  -Wformat=2
  -Wmisleading-indentation
  -Wmissing-include-dirs
  -Wnull-dereference
  -Wswitch-enum
  -Wunused
  -Wunused-variable
  -Wunused-parameter
  -Wvla
  # Warnings that are not allowed in C compilation units
  $<$<COMPILE_LANGUAGE:CXX>:-Wnon-virtual-dtor>
  $<$<COMPILE_LANGUAGE:CXX>:-Woverloaded-virtual>
)
```

## GCC specific

```
if (CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    message("Adding extra GCC only compilation options")
    add_compile_options(
      -Wduplicated-cond
      -Wframe-larger-than=1000000
      -Wlogical-op
      -Wduplicated-branches
      -Wformat-overflow=2
      -Wno-shadow-field-in-constructor
      # We allow these as they are known issues; this is how you can have 'warnings' for these issue, even is '-Werror' is enabled.
      # This is usefull when you add these options to older projects and you have not fixed all the warnings yet.
      -Wno-error=format-overflow=
      -Wno-error=useless-cast
      # Warnings that are not allowed in C compilation units
      $<$<COMPILE_LANGUAGE:CXX>:-Wvolatile>
    )
endif()
```

## Clang specific options

```
if (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  message("Adding Clang suppressions")
  add_compile_options(
      -Wcomma
      # this very noisy and these warnings are coming from third-party headers (for me)
      -Wno-gnu-zero-variadic-macro-arguments
      -Wno-gnu-line-marker
  )
endif()
```

## MSVC

```
/W4 /w14242 /w14254 /w14263 /w14265 /w14287 /w14289 /w14296 /w14311 /w14545 /w14546 /w14547 /w14549 /w14555 /w14640 /w14826 /w14905 /w14906 /w14928
```

