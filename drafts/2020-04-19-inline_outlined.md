---
layout: post
title: "C++ - Outlined inline"
author: Razakhel
categories: cpp
tags: cpp modern
---

The [_inline_](https://en.cppreference.com/w/cpp/language/inline) keyword has been around since forever in C++, and its meaning has always been subject to misunderstandings. This article is dedicated to help understand its usage & effects.

## What does _inline_ mean?

The main confusion comes from the actual meaning of _inline_.

## Inlining a function

> An inline function has the same cost as a macro.

A well known behavior of the `inline` keyword is that it tells the compiler to .

However, this is merely a hint: inlining a function is absolutely not mandatory, and the decision is **entirely** up to the compiler, in both ways (a function may be inlined even without the specifier). Some might even totally ignore whether the keyword is specified or not[^gcc-clang-check-inline].

There are ways to force the compiler to inline a function[^msvc-relative-force-inline], but they are compiler-specific:

```cpp
#if defined(__clang__) || defined(__GNUC__) // Clang or GCC
#   define ForceInline __attribute__((always_inline))
#elif defined(_MSC_VER) // MSVC
#   define ForceInline __forceinline
#else // Other compilers
#   define ForceInline
#endif
```

([Live example](https://godbolt.org/z/dkDu26), where GCC & Clang do remove the function, but MSVC doesn't)

The other way around is also possible, to force inlining to be disabled:

```cpp
#if defined(__clang__) || defined(__GNUC__) // Clang or GCC
#   define NoInline __attribute__((noinline))
#elif defined(_MSC_VER) // MSVC
#   define NoInline __declspec(noinline)
#else // Other compilers
#   define NoInline
#endif
```

## Linkage specifics



## Implicit inlining

There are cases where `inline` is totally implicit, thus redundant:
- If a member function is entirely defined in the class definition

## Additional informations

- GCC
  - [Documentation on _inline_](https://gcc.gnu.org/onlinedocs/gcc/Inline.html)
  - GCC has a dedicated flag `-fno-inline-small-functions` to remove inlining on _all_ small functions.



---

[^gcc-clang-check-inline]: GCC and Clang do still [take `inline` into account](https://blog.tartanllama.xyz/inline-hints/).

[^msvc-relative-force-inline]: With MSVC, `__forceinline` [doesn't _force_ inlining per se](https://docs.microsoft.com/en-us/cpp/cpp/inline-functions-cpp#inline-__inline-and-__forceinline), but is a stronger hint.

[^weekly-cpp-inline]: See Jason Turner's C++ Weekly about inline: https://www.youtube.com/watch?v=GldFtXZkgYo
