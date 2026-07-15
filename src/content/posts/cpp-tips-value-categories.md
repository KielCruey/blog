---
title: 'C++ Value Categories'
published: 2026-7-24
draft: false
description: 'Tips for C++: Looking into more complicated references types and how they are used.'
series: 'Cpp Tips'
tags: ['cpp', 'c++98']
---

# C++ Tip Series
For all the code in this series, the repository is located [here](https://github.com/KielCruey/cpp-tips) in the 'cpp-tips' directory. Look for the directory with the same name as the article's title.

## C++ Value Categories
When you have a C++ expression, which is a sequence of non-trivial values and operators, that expression is able to be evaluated. Programming languages like C++ need to collect information from these expressions and then able to do something with it, whether that's something logical or a computation.

In C++ programming, that expression is broken into two independent and distinguishable properties (Value Categories - cppreference.com, n.d.): 
* [type](https://en.cppreference.com/cpp/language/type)
* [value category](https://en.cppreference.com/cpp/language/value_category) (prvalue, rvalue, xvalue, glvalue, and lvalue)

### Hierarchy of Value Categories 
![Value Categories](../post-diagrams/cpp-tips/value-categories/value-categories.png 'C++ Value Categories')

In the figure above, C++ breaks down all the value categories into these properties (McNellis, 2010):
* An <u>lvalue</u> (historically, could appear on the left-hand side of an assignment expression) designates a function or an object.
* An <u>xvalue</u> (an “eXpiring” value) also refers to an object, usually near the end of its lifetime (so that its resources may be moved, for example). An xvalue is the result of certain kinds of expressions involving rvalue references.
* A <u>glvalue</u> (“generalized” lvalue) is an lvalue or an xvalue.
* An <u>rvalue</u> (historically, could appear on the right-hand side of an assignment expression) is an xvalue, a temporary object or subobject thereof, or a value that is not associated with an object. If it's not a lvalue, it's a rvalue.
* A <u>prvalue</u> (“pure” rvalue) is an rvalue that is not an xvalue.

## Brief History of Value Categories
Since the being of C and, effectively, also the beginning of C++, lvalues existed in the conception of the C++ core language. [CPL](https://en.wikipedia.org/wiki/CPL_(programming_language)), which is an early ancestor of [C](https://en.wikipedia.org/wiki/C_(programming_language)), used the idea and verbiage of "left-hand mode" or lvalue and "right-hand mode" or rvalue. Historically, lvalue or rvalue been associated with the left or right side of the ``=`` side in a C++ expression.

In ``c++98``, lvalues and rvalues became a commonly known category in C++.

lvalues has a location in memory, and a rvalue is a temporary value that will lose it's life-time scope.

however c++ starting added new keywords and concepts like ``const``, making not all lvalues appear on the left side of the ``=`` sign.


Summary of lvalues and rvalues (CppCon, 2019):
| value category        | can take the address of | can assign to |
|:----------------------|:------------------------|:--------------|
| lvalue                | yes                     | yes           |
| non-modifiable lvalue | yes                     | no            |
| non-class rvalue      | no                      | no            |

## Implications of Value Categories

lValue references a memory location
example, ``double& lValue = l;``

rValue references data
example ``double&& rValue = r;``

# References 
Value categories - cppreference.com. (n.d.). https://en.cppreference.com/cpp/language/value_category

McNellis, J. (2010, August 30). What are rvalues, lvalues, xvalues, glvalues, and prvalues? Stack Overflow. https://stackoverflow.com/questions/3601602/what-are-rvalues-lvalues-xvalues-glvalues-and-prvalues

GeeksforGeeks. (2025, July 15). lvalues references and rvalues references in C++ with Examples. GeeksforGeeks. https://www.geeksforgeeks.org/cpp/lvalues-references-and-rvalues-references-in-c-with-examples/

Zhang, R. (2020, July 3). The story of value categories in C++. Ray. https://oneraynyday.github.io/dev/2020/07/03/Value-Categories/

CppCon. (2019, October 2). Back to Basics: Understanding value categories - Ben Saks - CPPCon 2019 [Video]. YouTube. https://www.youtube.com/watch?v=XS2JddPq7GQ