---
title: 'C++ Loops'
published: 2026-5-10
draft: false
description: 'Tips for C++: Looking into all the C++ loops, but emphasizing the for-each/range-based loops which is a new feature in c++11.'
series: 'Cpp Tips'
tags: ['cpp', 'c++11']
---

# C++ Tip Series
For all the code in this series, the repository is located [here](https://github.com/KielCruey/cpp-tips) in the 'cpp-tips' directory. Look for the directory with the same name as the article's title.

## C++ Loops
For most mid-level to high-level programming languages have some sort of looping concept, which include languages like Python, C/C++, and Java to name a few. Loop have the ability to control how your program does something -- like manipulating data -- should be executed in a iterative or consecutive fashion. The execution of these loops are monitored by a condition, and will continue that execution until it isn't satisfied any more (Wikipedia contributors, 2026).

In C++, there are four different types of loops:
* while loops
* do-while loops
* for loops
* for-each/range-based

There's also an intermediate loop using iterators that acts like the for-each/range-based loop. By looking at the iterator based loop, it helps break down how the for-each/range-based loop is implemented. 

## C++ Loops' Syntax
``` c++ title="Loop Logic (GeeksforGeeks, 2026)"
// standard for loop
for( <initialization>; <condition>; <expression statement>) {
    // loop execution
}

// for-each/range-based loop
for(<range-declaration> : <range-expression>) {
    // loop execution
}

// while loop
while(<condition>) {
    // loop execution
}

// do-while loop
do {
    // loop execution
} while(<condition>);
```

We will see in followings examples that all the loops outputs are exactly the same, however how the execution and the process of how they do it is different. Depending on your needs, use the correct loop logic necessary.

### While Loop
For the while loop, here the logic and structure how it works.

![While Loop Logic](../post-diagrams/cpp-tips/loops/while-loop-flowchart.png 'While Loop Logic')

The while loop is the most basic loop. It allows the programmer to only check a condition and if that condition isn't satisfied, the execution loop will stop. However, the programmer has the responsibility for the test condition to change inside the loop. If the programmer forgets to modify the test condition, the loop can execute infinitely.

Since the test condition is checked first, there's a possibility the loop with never have a true condition and will never execute anything. In other words, the condition is check before entering the while loop.

### While Loop Example
``` c++ title="loops/while-loop.cpp"
#include <vector>

int main() {
    std::vector<int> v{4,6,8,2,1};

    int i{0};
    while(i < v.size()) {
        fprintf(stdout, "%i ", v[i]); // output: 4 6 8 2 1
        i++;
    }

    return 0;
}
```

### Do-While Loop
For the do-while loop, here the logic and structure how it works.

![Do-While Loop Logic](../post-diagrams/cpp-tips/loops/do-while-loop-flowchart.png 'Do-While Loop Logic')

Unlike the while loop, the do-while loop will execute once, and then the test condition is checked. Similarly to the while loop, the programmer is responsible for iterating the test condition. The test condition needs to be changed inside the loop's execution logic. If the programmer forgets to modify the test condition, the loop can execute infinitely.

In other words, the condition is check after entering the while loop. Which means the loop test condition maybe false, if so then it will run the loop only once.

### Do-While Loop Example -- Standard Usage
``` c++ title="loops/do-while-loop.cpp"
#include <vector>

int main() {
    std::vector<int> v{4,6,8,2,1};

    int i{0};
    do {
        fprintf(stdout, "%i ", v[i]); // output: 4 6 8 2 1
        i++;
    } while(i < v.size());

    return 0;
}
```

### Do-While Loop Example -- One Time Execution
``` c++ title="loops/testing-loop-logic.cpp"
#include <iostream>

int main() {
    // prints the initial "Hello World!" and exits
    fprintf(stdout, "do-while loop:\n");
    int i = 0; // test condition evaluated to false before entering the loop
    do {
        fprintf(stdout, "Hello World!");
    } while(i > 0);

    return 0;
}
```

## For Loop
For the standard for loop, here the logic and structure how it works.

![For-Loop Logic](../post-diagrams/cpp-tips/loops/for-loop-flowchart.png 'For-Loop Logic')

When creating a for loop, the initialization of "dummy" variable -- usually an int -- is instantiated and the condition is checked. The loop is executed and THEN the expression is revaluated (incremented or decremented). So the evaluation is before the execution and the update of expression statement is after the loop execution.

### For Loop Example
``` c++ title="loops/for-loop.cpp"
#include <vector>

int main() {
    std::vector<int> v{4,6,8,2,1};

    for(int i = 0; i < v.size(); i++) {
        fprintf(stdout, "%i ", v[i]); // output: 4 6 8 2 1
    }

    return 0;
}
```
## For-Each/Range-Based Loop
For the for-each/range-based loop, here the logic and structure how it works.

![For-Each-Loop Logic](../post-diagrams/cpp-tips/loops/for-each-loop-flowchart.png 'For-Each-Loop Logic')

However before we look into the for-each loop, let's take a look at the iterative for loop, which uses a container iterator to print out the contents of the container.

Think of an iterator as a pointer -- which that pointer does have properties, but we won't worry about that at the moment -- assigned to the container's elements. The iterator points to the container's address. Just like using any pointer, the pointer uses the "*" operator to dereference it and gets the content.

``` c++ title="loops/iterative-for-loop.cpp"
#include <vector>

int main() {
    std::vector<int> v{1,2,3,4,5};

    for(std::vector<int>::iterator it = v.begin(); it != v.end(); it++) {
        fprintf(stdout, "%i " ,*it); // *it dereferences v's content
    }

    return 0;
}
```

The for-each/ranged-based loop does exactly as the iterative for loop, but the syntax is simplified. 

### For-Each/Range-Based Loop Example -- Pass-by Value and Pass-by Reference
Let's take a look at pass-by value and pass-by reference implementations.

``` c++ title="loops/for-each-loop.cpp"
#include <vector>
#include <iostream>

int main() {
    std::vector<int> v{4,6,8,2,1};

    fprintf(stdout, "Pass-By Value\n");
    // multiplying the content of the vector by 2
    for(int i : v) {
        i = 2 * i;
        fprintf(stdout, "%i ", i); // output: 8 12 16 4 2
    }

    std::cout << std::endl;

    // vector's content didn't change!
    for(int i : v) {
        fprintf(stdout, "%i ", i); // output: 4 6 8 2 1
    }

    std::cout << std::endl;

    fprintf(stdout, "Pass-By Reference\n");
        // multiplying the content of the vector by 2
    for(int& i : v) {
        i = 2 * i;
        fprintf(stdout, "%i ", i); // output: 8 12 16 4 2
    }

    std::cout << std::endl;

    // vector's content did change!
    for(int i : v) {
        fprintf(stdout, "%i ", i); // output: 8 12 16 4 2
    }

    return 0;
}
```

## Summary
For-each/range-based for loop is a c++11 feature that simplifies the syntax of how C++ loops things. Because many other languages like C#, Java, and Python have the concept of the for-each loop, C++ modernized to do the same.

Using the for-each/range-based is preferred over using the iterative for loop syntax, and many times the expressing an iterator can be a clunky way of expressing it inside a for loop. Most of the time, the keyword ``auto`` is used to replace the iterator's syntax. Luckily, we have the for-each/range-based to keep our life's simple.

## Cited Work
Wikipedia contributors. (2026, May 4). Loop (statement). Wikipedia. https://en.wikipedia.org/wiki/Loop_(statement)

GeeksforGeeks. (2026, March 31). for Loop in C++. GeeksforGeeks. https://www.geeksforgeeks.org/cpp/cpp-for-loop/