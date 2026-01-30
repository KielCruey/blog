---
title: 'The Compiling Process'
published: 2026-01-23
draft: false
description: 'Examining how compiling work for compiled languages like C++. Going through the process how your machine takes in files and produces a binary.'
tags: ['compiling']
---

Compiling a program can be seen a simple process that, to most people, something magically happens on your computer. It appears to be an easy process that doesn't take much thought, because look at it automatically happens before your eyes. Just put your source code in your favorite IDE and press build. Boom, there it goes, an executable, dynamic/static library gets created, or some other build target.

Let's take a look what really happens under the hood of the compilation car and take a few hot laps around the compiling track. We might need to a pit-stop to add more fancy parts to our target racecar. Okay, I will stop with the analogies. :smile:


## Compiling Stage Overview
Refer to this <u>**Figure 1**</u> below:
![compiling-process](../post-diagrams/compiling-process/compiling.png 'Figure 1: Compiling Stages')

 So in general, the "compilation stage" is different in C/C++. For example other interpreted languages, like Python don't have the "assembler" step. They use a virtual machine to create machine code. However, we aren't looking into interpreted languages build processes.

 





## Preprocessor Stage
The preprocessor stage is quite simple. The compiler will look into the code and figure out any statements with the "#" symbol and evaluate it. The most common are #include, #define, #if, and #ifdef but of course there for [C](https://www.cprogramming.com/reference/preprocessor/) and [C++](https://www.geeksforgeeks.org/cpp/cpp-preprocessors-and-directives/).

Now let's look at a "Hello World" example program.
```c++ title="hello-world/hello-world.cpp"
#include <cstdio>
#include <cstdlib> // EXIT_SUCCESS

int main()
{
  std::printf("Hello World\n");
  return EXIT_SUCCESS;
}
```

```shell title="creating .i file commands"
g++ -E hello-world.cpp -o hello-world.
```
If you open up the .i file, we see that the [cstdio source code](https://github.com/gcc-mirror/gcc/blob/master/libstdc++-v3/include/c_global/cstdio) is copied and pasted in the .cpp file. Instead of using the 


## Linking Stage
Unfortunately, we need to define some definitions, because words matter and they help specify important ideas. It makes our life easier as they are works used in the industry.

The first is a ***translation unit***. As we know in C++, we have header files (.hpp or .h) and we have source files (.cpp). Really these files are meaningless to the compiler for these exist to help humans partition code. What the compiler cares about are translation units, which are an object file with the header and source files combined into one file without the preprocessor "#include"s. The compiler will quite literally copy and paste all the "#include" header's text into object file. These files have an .o -- for Linux/macOS -- or .obj -- for Windows -- extension.

## optimization

## linking stage
https://www.youtube.com/watch?v=H4s55GgAg0I



## Resources

https://www.youtube.com/watch?v=3tIqpEmWMLI

[Compiling Articles](https://www.reddit.com/r/cpp/comments/khnng2/a_tutorial_for_absolute_beginners_to_understand/)


https://stackoverflow.com/questions/1106149/what-is-a-translation-unit-in-c