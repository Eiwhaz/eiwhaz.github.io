---
layout: post
title: "CMake - [C]Make your project right"
author: Razakhel
categories: cmake
tags: cmake tutorial
---

This article is a tutorial about how to setup your own project from scratch using [CMake](https://cmake.org/).

### Why CMake?

I'll state it right off the bat since it's often misunderstood: CMake does **not** compile anything. It simply is a build system, generating files which, in turn, are themselves dedicated to the compilation (Makefiles, Visual Studio solution, etc).

There certainly are build systems other than CMake, like [Premake](https://premake.github.io/), [Meson](https://mesonbuild.com/) and [qmake](https://doc.qt.io/qt-5/qmake-manual.html), among others. All of them are good and have been used for many years. So why should you choose CMake?

CMake is a well-known build system, used by a huge amount of projects. Many, many resources can be found to set up a project with CMake. To be honest, the main reason to adopt CMake over others is that it is so widely spread, it has become kind of a de facto standard system.

However, CMake's syntax is known to be pretty ugly, some would even say unreadable. I, for one, don't really mind the syntax, but I do dislike the fact that there are almost always several means to do the same thing.

CMake has evolved quite a bit throughout the years. Many operations from the olden days are now advised against, or even downright deprecated, and chances are you will find those on the internet. This article is not meant to explain the differences, neither will I call it "modern", because it has been years since this "modernity" has been available.

And this is exactly the purpose of this article: to show a way to use it, the one which, as far as I know, is considered the cleanest[^cleanest-but-incomplete].

Now let's get on to it!

### Preparation

CMake is not restricted to C++: it can handle projects in C, ObjectiveC, Fortran and [others](https://stackoverflow.com/a/44477728/3292304). But for this tutorial, I will stick to C++ and walk you through a basic C++ project.

The basic thing to setup a CMake project is to create a CMakeLists.txt file containing these 2 lines at the beginning:

```cmake
cmake_minimum_required(VERSION 3.10)

project(Tutorial)
```

- [`cmake_minimum_required()`](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html) defines the minimal CMake version needed to generate your project. The absolute minimal should be 3.1, but I usually upgrade the version when I discover an useful feature that does not exist in the current one. That version, at the time of writing, is 3.10, but the most up-to-date is the 3.18.3. Feel free to pick whatever version you deem fine for your project.
- [`project()`](https://cmake.org/cmake/help/latest/command/project.html) declares your project name.

## Targets

CMake revolves around the principle of targets. They are translated as solution projects with Visual Studio, Makefile targets when generating Makefiles, and so on. Targets can either be a library, an executable, or a custom action.

```cmake
# Adds an executable target named 'ExecTest', compiling the file 'main.cpp'
add_executable(ExecTest main.cpp)

# Adds a static library (.lib/.a) target named 'LibTest', compiling the file 'Lib.cpp'
add_library(LibTest STATIC Lib.hpp Lib.cpp)

# Same thing, but creates a dynamic library (.dll/.so)
add_library(LibTest SHARED Lib.hpp Lib.cpp)
```

A target can also be created with [add_custom_target()](https://cmake.org/cmake/help/latest/command/add_custom_target.html).

_Note_: the header 'Lib.hpp' has voluntarily been added as a source for the target. A header is obviously not compiled, but adding one to the sources allows it to be referenced in IDEs as a file being in the project. If this is not done, there may be no syntactic highlighting, no code verification (linting), or even won't be accessible via the IDE. It is thus advised to always add the project's used headers to the target's sources.

## Scope

Scopes are a really important concept of CMake's features. These scopes are defined by the target_*() instructions.

- PRIVATE: acts **only** on the current target. Nothing is propagated to the other targets depending on it.
- INTERFACE: acts **only** on the targets depending on the current one. Everything is propagated to the other targets depending on it.
- PUBLIC: combines PRIVATE & INTERFACE, acting on the current target _and_ on the ones depending on it.

The INTERFACE scope is special: it can only be applied on a target which has also been defined as INTERFACE. It can then be used as a way to propagate parameters and configurations.

## Chain targets

The main purpose of targets is to split the architecture, by creating a dependency chain between each other. This is done with [target_link_libraries()](https://cmake.org/cmake/help/latest/command/target_link_libraries.html):

```cmake
add_library(LibTest STATIC Lib.hpp Lib.cpp)

add_executable(ExecTest main.cpp)
target_link_libraries(ExecTest LibTest)
```

Here, ExecTest depends on LibTest: the latter will then be compiled first. In addition, ExecTest will benefit from all attributes defined as PUBLIC or INTERFACE on LibTest (include paths, definitions, compilation options, etc).

## Chain CMakeLists

### [add_subdirectory](https://cmake.org/cmake/help/latest/command/add_subdirectory.html)



### [include](https://cmake.org/cmake/help/latest/command/include.html)



## GLOB or not GLOB?

There have been huge debates about whether to use the GLOB & GLOB_RECURSE directives or not. Even the official documentation [does not recommend using it](https://cmake.org/cmake/help/latest/command/file.html#filesystem). However, I do not think this is an evil usage, or at the very least shouldn't be mindlessly demonized.

Let's be clear: everything I will say in this entire section will be **entirely** my opinion and my own. I will not recommend anything and will let you judge on your own.

#### TODO

In my opinion, using GLOB is a 50/50 trade-off. Some action has to be done in both cases anyway, whether it is to add a line in the CMakeLists or reload it manually; I might as well pick the more readable & convenient one to me.

---

[^cleanest-but-incomplete]: Cleanest, but not exhaustive. There are many features which I don't completely know about and/or which I deem unnecessary to write about in an article focused on CMake's basic understanding. However, these features are not in contradiction with everything that is explained in this article.
