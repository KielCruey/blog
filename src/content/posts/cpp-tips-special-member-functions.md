---
title: 'C++ Special Member Functions'
published: 2026-7-11
draft: false
description: 'Tips for C++: Looking into C++ Rules of "Zero", "Three", "Five", and "Seven".'
series: 'Cpp Tips'
tags: ['c++98', 'c++11', 'rule of zero', 'rule of three', 'rule of five', 'rule of six', 'rule of seven']
---

# C++ Tip Series
For all the code in this series, the repository is located [here](https://github.com/KielCruey/cpp-tips) in the 'cpp-tips' directory. Look for the directory with the same name as the article's title.

# Brief History of Special Member Functions
All classes have hidden special member functions when you define a class, which help with memory management, life-time and class behaviors. The compiler automatically generates and define if and when they are used, however the programmer may explicitly define them.

We will be looking into these functions as they are called special member functions.

For ``c++98``, only four special member functions where available: 
* default destructor
* destructor
* copy constructor
* copy assignment operator

Then in ``c++11``, the two more special member functions were added:
* move constructor
* move assignment operator

## Special Member Function Breakdowns
Let's examine the special member functions, and comment about the importance of each class's function. 

### Constructor
The purpose of the constructor is to create and initialize the resources for that particular object. It gives the object its original values during the object creational process.

### Destructor
The purpose of the destructor is to release of the resources from a particular object. The destructor tells the object how exactly free member variable's memory space.

### Copy Constructor
The purpose of the copy constructor is to copy all the member variables of a current object into a newly created object. By default, the copy constructor will perform a shallow copy, which the new object will only reference the old object's member variables. 

However, if the user definition is implemented correctly, the copy constructor will perform a deep copy.

### Copy Assignment Operator
The purpose of the copy assignment operator is to copy all the member variables already two existing "equivalent class" objects. Effectively it will rewrite over one object's data. 

### Move Constructor
The purpose of the move constructor is to transfer ownership of one object's data to another newly created object's data. Unlike the copy constructor that does an expensive deep copy, the move constructor hands-off the data, which is a much faster process. 

It's important to note that this process doesn't delete any objects, but it does make the data of the transferred object all null.

The idea of a temporary object, or an unnamed object, will be used.

### Move Assignment Operator
The purpose of the move assignment operator is to transfer all the object's member variables to another existing object's member variables.

## Rule of X
### Rule of Zero
don't define any of the special member functions

### Rule of Three
* destructor
* copy constructor
* copy assignment operator

### Rule of Five
* destructor
* copy constructor
* copy assignment operator
* move constructor
* move assignment operator

### Rule of Six
* default destructor
* destructor
* copy constructor
* copy assignment operator
* move constructor
* move assignment operator

### Rule of Seven
* default destructor
* destructor
* copy constructor
* copy assignment operator
* move constructor
* move assignment operator
* friend swap

# Resources
C++: rule of 0, 3, 5, and 6. (n.d.). Gist. https://gist.github.com/MangaD/c00f23c66156fec4922c4d6ea6da234b