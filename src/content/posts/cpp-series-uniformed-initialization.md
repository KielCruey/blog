---
title: 'Uniform Initialization'
published: 2026-5-7
draft: false
description: 'Tips for C++: Looking into initialization, and how to implement it via the modern approach.'
series: 'Cpp Tips'
tags: ['cpp', 'c++11']
---

# C++ Tip Series
For all the code in this series, the repository is located [here](https://github.com/KielCruey/cpp-tips) in the 'cpp-tips' directory. Look for the directory with the same name as the article's title.

## What is Uniform Initialization?
Uniform Initialization is a new feature in C++11 that allows a different syntax to initializing variables and primitive types by using the braces {} instead of the parenthesis () (GeeksforGeeks, 2023). However, it isn't just another trivial way to initialize, it also has the mechanism to enforce safety checks.

The uniform initialization safety checking characteristics are:
* Prevents type conversation narrowing
* Guarantees initialization

## C++ Initialization
Throughout C/C++'s history, initialization has been categorized into seven different groups (Meeting Cpp, 2019):
* Default Initialization (no initializer)
* Copy Initialization ('= value', pass-by-value, return-by-value)
* Aggregate Initialization ('= {args}')
* Direct Initialization (argument list in parentheses)
* Value Initialization (empty parents)
* List Initialization ('{args}' in direct-list-init, '= {args}' in copy-list-init)
* Uniform Initialization ('{value}', {})

There are multiple ways of initialization variables and classes, here are just a few examples:

```c++ title="Types of Initialization"
int x; // default initialization
int x = 10; // value/direct initialization
int x = &y; // copy initialization
int x[3] = { 4,5,6 } // aggregate initialization
int x(7); // direct initialization
int x{}; // uniform initialization
int x{10}; // uniform initialization 

X x1(); // default constructor
X x2(1); // parameterized constructor
X x1 = x2; // copy-constructor initialization

std::vector<int> vector{ 1,2,3,4,5 } // initializer list
```

## Examples of Safety Checks
Let's take a look at type conversation narrowing and guarantees initialization and the safety part of uniform initialization.

### Prevents Type Narrowing
What is type narrowing? It's the process of taking a broader type and converting it into a more specific type, which usually leads to loss of type information.

```c++ title="Type Narrowing Examples"
int main() {
	int a{2}; // okay
	int b = 3.9; // okay -- but type narrowing occurs
	int c(4.2); // okay -- but type narrowing occurs
	int d{2.3}; // error -- narrowing from double to int
	
	return 0;
}
```

![Type Narrowing](../post-pictures/cpp-tips/uniform-initialization/uniform-initialization.PNG 'Type Narrowing')

### Guarantees Initialization
Unlike any other initialization, uniformed initialization will promise a zero or NULL ("0") if nothing is declared.

Here are some examples:
```c++ title="Guarantees Initialization Examples"
int main() {
    int a; // okay -- will be populated with any unknown int value
    int b(); // nothing gets initialized
    int c(3); // okay
    int d{}; // okay -- guarantees an initialized value of "0"
    int e{5}; // okay
	
    return 0;
}
```

![Debugging Results](../post-pictures/cpp-tips/uniform-initialization/guarantees-initialization-debugging.PNG 'Debugging Results')

As we can see from the "debugging results" figure, variable "a" happens to be initialized with an integer value of "387". C++ doesn't promise any particular value, so it could be any integer value. Additionally, variable "b" wasn't created at all, which is an issue related to C++ safety. Using the uniform initialization prevents these errors from occurring.

## Summary
Use uniform initialization whenever possible, because initializing variables or classes with "garbage" values or completely nothing is an issue you want to avoid. Uniform initialization will throw a compiling error if something is wrong and catching it before it turns into a run-time error. Moreover, C++ has an ideology to prevent or eliminate undefined behaviors and uniform initialization supports "safe, healthy, and efficient" programming (Safe C++, 2024).

# Resources
GeeksforGeeks. (2023, February 13). Uniform initialization in C++. GeeksforGeeks. https://www.geeksforgeeks.org/cpp/uniform-initialization-in-c/

Meeting Cpp. (2019, January 26). Initialization in modern C++ - Timur Doumler - Meeting C++ 2018 [Video]. YouTube. https://www.youtube.com/watch?v=ZfP4VAK21zc

Safe C++. (2024, September 11). https://safecpp.org/draft.html