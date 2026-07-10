---
title: 'C++ Special Member Functions'
published: 2026-7-11
draft: false
description: 'Tips for C++: Looking into C++ Rules of "Zero", "Three", "Five", and "Seven".'
series: 'Cpp Tips'
tags: ['c++03', 'rule of zero', 'rule of three', 'rule of five', 'rule of six', 'rule of seven']
---

# C++ Tip Series
For all the code in this series, the repository is located [here](https://github.com/KielCruey/cpp-tips) in the 'cpp-tips' directory. Look for the directory with the same name as the article's title.

## Brief History of Special Member Functions
``c++98`` special member functions where: 
* default destructor
* destructor
* copy constructor
* copy assignment operator

in c++11, the following special member functions were added:
* move constructor
* move assignment operator

## Special Member Functions

## Rule of Zero
don't define any of the special member functions

## Rule of Three
* destructor
* copy constructor
* copy assignment operator

## Rule of Five
* destructor
* copy constructor
* copy assignment operator
* move constructor
* move assignment operator

## Rule of Six
* default destructor
* destructor
* copy constructor
* copy assignment operator
* move constructor
* move assignment operator

## Rule of Seven
* default destructor
* destructor
* copy constructor
* copy assignment operator
* move constructor
* move assignment operator
* friend swap