---
title: 'C++ Smart Pointers'
published: 2026-7-9
draft: false
description: 'Tips for C++: Looking into smart pointers, their history, giving a brief examples of common memory management issues, and how c++11 modernized past smart pointers flaws.'
series: 'Cpp Tips'
tags: ['cpp', 'c++98', 'c++11', 'c++17', 'cpp-check']
---

# C++ Tip Series
For all the code in this series, the repository is located [here](https://github.com/KielCruey/cpp-tips) in the 'cpp-tips' directory. Look for the directory with the same name as the article's title.

## A Brief History of Smart Pointers
Smart pointers were influenced by C++'s lack of garbage collection mechanism criticized by other popular languages like C#, Java, and Python. C++ responded by modernizing the language by introducing ``std::auto_ptr`` in c++98. Then in c++11, ``std::unique_ptr``, ``std::shared_ptr``, and ``std::weak_ptr`` pointers were published into the language. These smart pointers piggybacked on the ``c++98`` idea of ``std::auto_ptr``, and attempting to make smart pointers a little bit more robust.

Then in``c++17``, ``std::auto_ptr`` was depreciated -- due to safety reasons -- from the language and actively discouraged by C++ standard committee. C++ prides itself for always being backwards compatible, and the C++ committee recommends substituting ``std::auto_ptr`` with ``std::unique_ptr`` for ``c++17`` and onward.

## Supporting Topics Related to Smart Pointer
Before we examine smart pointers, we need to look into some supporting topic like static memory and raw pointers.

### Static Memory
Before we talk about smart pointers, we need to understand static memory. Unlike dynamic memory, static memory doesn't need the programmer's intervention to implement. The scope of the memory gets created and destroyed automatically on the stack.

With static memory, it's all about scope. After the variable's lifetime is outside of the scope, the program will automatically free the data.

Here's an example:
``` c++ title="static-memory/static-memory.cpp"
#include <iostream>

int main() {
    int x = 2;
    fprintf(stdout, "x: %ld\n", x);

    // creating a new scope
    {
        int y = 2 * x;
        fprintf(stdout, "y: %ld\n", y);
    } // y get deleted here automatically

    // y can't be printed out because program deletes it from the stack
    // fprintf(stdout, "Value: %ld\n", y); // can't do this
    fprintf(stdout, "x: %ld\n", x);

    return 0;
}
```

The program takes care of the deletion of the memory on the stack in the case of static compile-time memory. Smart pointer share this fundamental idea of scoping, not the stack, but on the heap during run-time.

### Proper Use of a Raw Pointer
Next is raw pointers, which have been in the languages since the beginning. Unlike smart pointers, raw pointer require you to create and free memory as well as manage the lifetime of the scope. Using the ``new``/``new[]`` or ``malloc``/``calloc`` and ``delete``/``delete[]`` keywords depending on the data type and if you're using C or C++ style of memory allocation. 

Here's an example:
``` c++ title="raw-pointer/raw-pointer.cpp"
#include <iostream>

int main() {
    int *value = new int{42}; // dynamic memory created
    fprintf(stdout, "Value: %i", *value);

    int temp = 2 * (*value); // manipulating the value
    fprintf(stdout, "Value: %i", temp);

    // with raw pointers, you must delete it when leaving the scope or a memory leak will occur
    delete value;

    return 0;
```

Notice that the ``delete`` keyword is used once the programmer decides when the usage of `value` isn't needed anymore. The programmer must maintain the usage of the delete keyword throughout the program or memory leaks will occur. However with smart pointers, maintaining the ``delete`` isn't necessary anymore!

## Use Cases of Smart Pointers
Smart pointers prevents the following issues:
* eliminates deleting a pointer twice
* prevents dangling pointers from occurring
* decreases certain memory leaks

Smart pointers are basically a rudimentary form of garbage collection and eases the programmer from manually managing every single part of computer memory. Smart pointers ultimately makes C++ safer to program.

### Deleting a Pointer Twice
Trying to delete a pointer twice, which can occur in large codes bases, will cause and error. The program expects to delete the resource, but nothing is there, the program sees this undefined behavior and crashes.

Here's an example:
``` c++ title="deleting-pointer-twice/deleting-pointer-twice.cpp"
int main() {
    int *pointer = new int{100}; // creating a pointer

    delete pointer; // allowed -- resource deleted
    delete pointer; // error -- program crashes during run-time

    return 0;
}
```
Because the compiler doesn't catch this logical issue and effectively causes the program to crash on run-time. This should always be avoided. Smart pointers will prevent the programmer from accidentally deleting the pointer twice. 

### Dangling Pointers
A dangling pointer occurs when a pointer references a memory address that has been deleted by another pointer. 

Let's illustrate this example:
![Dangling Pointer](..\post-diagrams\cpp-tips\smart-pointers\dangling-pointer.png 'Dangling Pointer -- Red Arrow means Deleted Reference')

As we can see in the figure above, we have "pointer1" and "pointer2" pointing to "object1" in the "No Dangling Pointer" subgraph. Let's suppose that "pointer1" deletes the memory address its pointer to. In the "A Dangling Pointer" subgraph, that action causes "pointer2" to become a dangling pointer. It references something that doesn't exist or has a "dangling" reference.

Dangling pointers is a sign of memory leak, however we can avoid dangling pointer memory leaks by implementing ``std::weak_ptr`` at opportunistic situations. More on ``std::weak_ptr`` later.

### Memory Leaks
In the context of memory leaks, continuously creating a heap-allocated raw pointer without ever deleting the memory, is a form of stability memory leak. The memory keeps on growing without dellocating the resources effecting the the long-term usability.

Here's an example:
``` c++ title="memory-leak/memory-leak.cpp"
#include <iostream>

int main() {
    {
        // creating value
        int *value = new int{42};
        fprintf(stdout, "Value: %i\n", *value);

        // memory leak -- didn't delete or free out-of-scope memory 
    }

    return 0;
}
```

:::important
It's known that the program executes and finishes its process, the memory will be given to the operating system. Most likely the OS will free the memory, however don't count on it as stated by this [Reddit](https://www.reddit.com/r/cpp_questions/comments/mkayrg/does_the_heap_clear_once_the_program_finishes/) post. It's always good practice to free/deallocate all your program's memory before the program terminates.
::: 

Now lets use [Cpp-Check](https://cppcheck.sourceforge.io/) -- a static analyzer -- to verify that a memory leak occurred.
![memory-leak](../post-pictures/cpp-tips/smart-pointers/memory-leak.PNG 'Memory Leak Occured')

As we can see, a memory leak occurred, however using smart pointers and help eliminate manual memory management and prevents memory leaks when the programmer forgets to use the ``delete`` keyword at important moments. In this case, ``delete`` wasn't used at all.

Smart pointers effectively help with memory leak issues. It's necessary for programs to have zero memory leaks. If memory isn't allocated and deallocated correctly, the program my crash due to the program's lack of available resources. Ultimately, affecting the reliability, performance, and stability of the program, and for embedded systems that may run for years without a reboot. (Wikipedia contributors, 2026)

In an article by [Kellett](https://www.softwareverify.com/blog/the-nineteen-types-of-memory-leak/#type16), there are 19 common memory leaks in C++. Kellett illustrates why and how they occur and a solution to fix them. Memory leaks are another topic related to smart pointers; however, we will take a look at some examples on the way, but only give some examples for purpose of this article. For more information on specific memory leaks, refer to Kellett or other references. (Kellett, 2025)

## Smart Pointers
As we've seen previously, memory leaks and program crashes can occur. However with smart pointers, it automatically deallocates objects resource or prevents fatal programming conditions and states. Smart pointers are a blessing for C++ safety!

In order to use the smart pointers, you must include ``<memory>`` library in your program, which gives you access to ``unique``, ``shared``, ``weak``, and ``auto`` pointers.

### Unique Pointer (``std::unique_ptr``)
Unique pointer is the ``c++11``'s revised definition of a smart pointer, which helps facilitate memory ownership. When using ``unique_ptr``, only <u>**one**</u> object can be the "owner" of the data explicit, which means it can't be copied, but it can be moved using ``std::move``. However, the "owner" has the responsibility to delete the object, and it will occur once the object is out of scope (GeeksforGeeks, 2025).

#### Unique Pointer Syntax
``` c++ title="Unique Pointer Syntax"
// pointer initialization
unique_ptr<SomeType> u;

// not preffered because doesn't implement exception safety
unique_ptr<SomeType> u(new <SomeType>);
unique_ptr<int> u(new int{1}); // example

// pointer initialization with (right side) new-expression -- exception safety enabled
unique_ptr<SomeType> u = std::make_unique<SomeType>(<constructor & parameters here>);
unique_ptr<int> u = std::make_unique<int>(int{1}); // example
```

#### Important Unique Pointer Member Functions
The following are some important member functions when using the ``std::unique_ptr``:
| Method                  | Description                                                                        |
|:------------------------|:-----------------------------------------------------------------------------------|
| ``pointer `` release()  | Releases the ownership of the managed object, if any.                              |
| ``void`` reset(``ptr``) | Replaces the current pointer with ``ptr``.                                         |
| ``void`` swap(``ptr``)  | Swaps the current object (and delete it) and changes unique_ptr object to ``ptr``. |
| ``pointer`` get()       | Returns a pointer to the managed object or ``nullptr`` if no object is owned.      |

#### Unique Pointer Member Function Examples
``` c++ title="unique-pointer/member-functions.cpp"
#include <memory>
#include <iostream>

int main() {
    // release() example
    std::unique_ptr<int> value1 = std::make_unique<int>(int{ 1 }); // value1 = 1
    fprintf(stdout, "value1: %d\n", *value1);
    value1.release(); // value1 = nullptr

    // reset() example
    std::unique_ptr<int> value2 = std::make_unique<int>(int{ 2 }); // value2 = 2
    fprintf(stdout, "value2: %d\n", *value2);
    // releasing the current pointer and replacing it with a new one of value 22
    value2.reset(std::make_unique<int>(int{ 22 }).release()); // value2 = 22
    fprintf(stdout, "value2: %d\n", *value2);

    // swap() example
    std::unique_ptr<int> value3 = std::make_unique<int>(int{ 3 }); // value3 = 3
    std::unique_ptr<int> value4 = std::make_unique<int>(int{ 4 }); // value4 = 4
    fprintf(stdout, "value3: %d\n", *value3);
    fprintf(stdout, "value4: %d\n", *value4);
    value3.swap(value4); // value3 = 4 , value4 = 3
    fprintf(stdout, "value3: %d\n", *value3);
    fprintf(stdout, "value4: %d\n", *value4);

    // get() example
    std::unique_ptr<int> value5 = std::make_unique<int>(int{ 5 }); // value5 = 5
    fprintf(stdout, "value5: %d\n", *value5);
    int* rawPtr = value5.get(); // rawPtr = 5
    fprintf(stdout, "rawPtr: %d\n", *rawPtr);

    return 0;
}
```

#### Unique Pointer Example -- Can't be Copied, but Move is Allowed
In this example, it shows that the unique pointer can't be copied but can be transferred:
``` c++ title="unique-pointer/unique-pointer.cpp"
#include <memory>
#include <iostream>

int main() {
    std::unique_ptr<int> value1 = std::make_unique<int>(int{ 10 });
    fprintf(stdout, "value1: %d\n", *value1);

    // compiling error can't copy value1 into value2
    // std::unique_ptr<int*> value2 = value1;

    // allowed
    std::unique_ptr<int> value2 = std::move(value1); // value1 = nullptr
    fprintf(stdout, "value2: %d\n", *value2);

    return 0;
}
```

### Shared Pointer (``std::shared_ptr``)
Shared pointer, unlike unique pointer, allows multiple "ownership", but once the pointer count goes to zero, then the object's memory is freed or deallocated. ``std::shared_ptr`` has some member variables/functions that internally increment/decrements the number of ``std::shared_ptr``'s references pointing to that memory address. In other words, there's an agreement that once there are no references, then the shared pointer will be automatically released. 

#### Shared Pointer Syntax
``` c++ title="Shared Pointer Syntax"
// pointer initialization
shared_ptr<SomeType> s;

shared_ptr<SomeType> s(new <SomeType>{});

// pointer initialization with (right side) new-expression
shared_ptr<SomeType> s = std::make_shared<SomeType>({constructor & parameters here});
```

#### Important Shared Pointer Member Functions
The following are some important member functions when using the ``std::shared_ptr``:
| Method                  | Description                                                                                                                    |
|:------------------------|:-------------------------------------------------------------------------------------------------------------------------------|
| ``void`` reset(``ptr``) | Replaces the managed object with an object pointed to by ``ptr``.                                                              |
| ``void`` swap(``ptr``)  | Swaps the current objects. Reference counts, if any, are not adjusted. |
| ``T*`` get()            | Returns the stored pointer, not the managed pointer.                                                                           |
| ``long`` use_count()    | Returns the number of shared_ptr objects referring to the same managed object.                                                 |
| ``bool`` unique()       | Checks whether the managed object is managed only by the current shared_ptr object.                                            |

#### Shared Pointer Member Function Examples
``` c++ title="shared-pointer/member-functions.cpp"
#include <memory>

int main() {
    // reset example
    std::shared_ptr<int*> value1 = std::make_shared<int*>(new int{42}); // value1 = 42
    std::shared_ptr<int*> value2 = std::make_shared<int*>(new int{24}); // value2 = 24
    value1.reset(value2.get()); // value1 = 42  value2 = 42

    // swap example
    std::shared_ptr<int*> value3 = std::make_shared<int*>(new int{ 1 }); // value3 = 1
    std::shared_ptr<int*> value4 = std::make_shared<int*>(new int{ 11 }); // value4 = 11
    value3.swap(value4); // value3 = 11  value4 = 1

    // get example
    std::shared_ptr<int> value0 = std::make_shared<int>(42); // value0 = 42
    int* rawPtr = value0.get(); // rawPtr = 42

    // use_count example
    std::shared_ptr<int*> value5 = std::make_shared<int*>(new int{ 5 }); // value5 = 5
    std::shared_ptr<int*> value6 = value5;
    std::shared_ptr<int*> value7 = value5;
    fprintf(stdout, "use_count: %d\n", value5.use_count()); // count = 3
    value7 = nullptr;
    fprintf(stdout, "use_count: %d\n", value5.use_count()); // count = 2
    value6 = nullptr;
    fprintf(stdout, "use_count: %d\n", value5.use_count()); // count = 1

    // unique example
    std::shared_ptr<int*> value8 = std::make_shared<int*>(new int{ 8 }); // value8 = 8
    std::shared_ptr<int*> value9 = value8;
    fprintf(stdout, "isUnique?: %d\n", value8.unique()); // 0 (false)
    value9 = nullptr;
    fprintf(stdout, "isUnique?: %d\n", value8.unique()); // 1 (true)

    return 0;
}
```

### Weak Pointer (``std::weak_ptr``)
Weak pointers can be thought as similar to a ``std::shared_ptr``, however there isn't a strong reference (has ownership) but a weak reference (doesn't have any ownership) to the memory address. What this means it's a non-owning pointer that doesn't increase the ``std::shared_ptr`` reference count.

Another use case of ``std::weak_ptr`` is to prevent cyclical dependencies. Let's say that object1 references object2 and object2 references object1. Let's say that object1 released its memory, so object2's reference is invalid and will reference a ``nullptr``. Manipulating object2 will make the program crash. 

However, using ``std::weak_ptr`` can eliminate this issue. By using ``std::weak_ptr``'s member function ``lock()`` and ``expired()``, we can check if a reference's memory has been released, and if it's been released, don't do anything.

We will look at a concrete example in the cyclical dependency section below.

#### Weak Pointer Syntax
``` c++ title="Weak Pointer Syntax"
// pointer initialization -- points to nothing
weak_ptr<SomeType> w;

// weak pointers must be initialized and reference to a shared_ptr
weak_ptr<SomeType> w(<SomeSharedPtr>);
weak_ptr<SomeType> w = <SomeSharedPtr>;
```

#### Important Weak Pointer Member Functions
:::note
There isn't a std::make_weak() function, because a weak pointer references a strong pointer. Think of a weak pointer as a supporting pointer and in-of-itself can't exist independently. They don't have their own identity.
:::

The following are some important member functions when using the ``std::weak_ptr``:
| Method                        | Description                                                                        |
|:------------------------------|:-----------------------------------------------------------------------------------|
| ``void`` reset(``ptr``)       | Replaces the managed object with an object pointed to by ``ptr``.                  |
| ``void`` swap(``ptr``)        | Swaps the current object (and delete it) and changes ``weak_ptr`` object to ``ptr``. |
| ``bool`` expired()            | Checks whether the referenced object was already deleted.                          |
| ``std::shared_ptr<T>`` lock() | Creates a ``shared_ptr`` that manages the referenced object.                           |
| ``long`` use_count()          | Returns the number of ``weak_ptr`` objects referring to the same managed object.       |

#### Weak Pointer Member Function Examples
``` c++ title="weak-pointer/member-functions.cpp"
#include <memory>
#include <iostream>

int main() {
    // reset() example
    std::shared_ptr<int> value1 = std::make_shared<int>( int{1} ); // value1 = 1 (1 strong)
    std::weak_ptr<int> wValue1{ value1 }; // wValue1 = 1 (1 strong, 1 weak)
    wValue1.reset(); // (1 strong)

    fprintf(stdout, "value1: %d\n", value1.use_count()); // count = 1
    fprintf(stdout, "wValue1: %d\n", wValue1.use_count()); // count = 0

    // swap() and lock() example
    std::shared_ptr<int> value2 = std::make_shared<int>(int{ 20 }); // value2 = 20    
    std::shared_ptr<int> value3 = std::make_shared<int>(int{ 30 }); // value3 = 30
    std::weak_ptr<int> wValue2{ value2 }; // wValue2 = 20
    std::weak_ptr<int> wValue3{ value3 }; // wValue3 = 30
    wValue2.swap(wValue3); // wValue2 = 30, wValue3 = 20

    fprintf(stdout, "wValue2: %i\n", *wValue2.lock()); // wValue2 = 30
    fprintf(stdout, "wValue3: %d\n", *wValue3.lock()); // wValue3 = 20

    // expired() example
    std::weak_ptr<int> wValue4; // wValue4 = nullptr

    // creating a new scope
    {
        std::shared_ptr<int> value4 = std::make_shared<int>(int{ 40 }); // value4 = 40
        wValue4 = value4; // wValue4 = 40

        if (!wValue4.expired()) std::cout << "wValue4 is valid\n";
        else std::cout << "wValue4 is expired\n";
    } // shared pointer get deleted -- out of scope

    if (!wValue4.expired()) std::cout << "wValue4 is valid\n";
    else std::cout << "wValue4 is expired\n";

    // use_count() example
    std::shared_ptr<int> value5 = std::make_shared<int>(int{ 5 }); // value5 = 5 (1 strong)
    std::weak_ptr<int> w1Value5{ value5 }; // w1Value5 = 5 (1 strong, 1 weak)
    std::weak_ptr<int> w2Value5{ value5 }; // w2Value5 = 5 (1 strong, 2 weak)

    // weak pointer does not increase the reference count, only shared pointer do.
    fprintf(stdout, "w1Value5 -- use_count: %d\n", w1Value5.use_count()); // count = 1
    w2Value5.reset(); // w2Value5 == nullptr (1 strong, 1 weak)

    // shared pointer increment count
    std::shared_ptr<int> s1value5 = value5; // value5 (2 strong, 1 weak)
    fprintf(stdout, "w1Value5 -- use_count: %d\n", w1Value5.use_count()); // count = 2

    return 0;
}
```

#### Weak Pointer Example -- Eliminating Cyclical Dependencies
As seen in the diagram below, follow the strong pointer (black arrow) until it reaches the end node. When you have strong pointers pointing to each other, they go around referencing themselves and continuing to find an ending. However you see there isn't one.

![cyclical-dependency-problem](../post-diagrams/cpp-tips/smart-pointers/cyclical-dependency-problem.png 'Cyclical Dependency Problem')

However in the solution case -- see the diagram below -- using a weak pointer breaks the cycle. Think of it that the red "weak" pointer is imaginary to the system, but you the programmer knows it's there. When you start at husband object, it stops at the wife object breaking the cycle.

![cyclical-dependency-solved](../post-diagrams/cpp-tips/smart-pointers/cyclical-dependency-solved.png 'Cyclical Dependency Solved -- Red Arrow Means "Weak" Pointer is Imaginary to the System')

#### Cyclical Dependency Problem Example
Illustrating strong pointers causes cyclical dependency.

``` c++ title="weak-pointer/cyclical-dependency-problem.cpp"
#include <memory>
#include <iostream>

class Wife;
class Husband;

class Wife
{
public:
	~Wife() { fprintf(stdout, "Wife destructor called\n"); }
	std::shared_ptr<Husband> pHusband;
};

class Husband
{
	
public:
	~Husband() { fprintf(stdout, "Husband destructor called\n"); }
	std::shared_ptr<Wife> pWife;
};

int main() {
	{
		std::shared_ptr<Husband> husband = std::make_shared<Husband>(); // (1 strong)
		std::shared_ptr<Wife> wife = std::make_shared<Wife>(); // (1 strong)

		// creating cyclical dependency
		husband->pWife = wife; // (2 strong)
		wife->pHusband = husband; // (2 strong)

		// use_count function counts strong ptrs
		fprintf(stdout, "wCount: %d\n", husband->pWife.use_count());
		fprintf(stdout, "hCount: %d\n", wife->pHusband.use_count());
	}

	// pointer never get delete outside this scope

    return 0;
}
```

#### Cyclical Dependency Solution Example
Using a weak pointer to solve cyclical dependency.

``` c++ title="weak-pointer/cyclical-dependency-solved.cpp"
#include <memory>
#include <iostream>

class Wife;
class Husband;

class Wife
{
public:
	~Wife() { fprintf(stdout, "Wife destructor called\n"); }
	std::weak_ptr<Husband> pHusband; // weak ptr
};

class Husband
{
public:
	~Husband() { fprintf(stdout, "Husband destructor called\n"); }
	std::shared_ptr<Wife> pWife;
};

int main() {
	{
		std::shared_ptr<Husband> husband = std::make_shared<Husband>();
		std::shared_ptr<Wife> wife = std::make_shared<Wife>();

		husband->pWife = wife; // (2 strong)
		wife->pHusband = husband; // (1 strong, 1 weak)

		// use_count function counts strong ptrs
		fprintf(stdout, "wCount: %d\n", husband->pWife.use_count());
		fprintf(stdout, "hCount: %d\n", wife->pHusband.use_count());
	}
	
	// 1 strong reference to husband, which cause the smart pointer to delete...
	// since the deletion of the husband reference decrements the wife reference by 1...
	// that then deletes the wife smart pointer

    return 0;
}
```

### Auto Pointer (``std::auto_ptr``)
Don't use auto_ptr, it was removed and depreciated from C++, because it's known to lead to errors. Seriously don't use it. However... there are some cases to use ``std::auto_ptr`` when working with C++ prior to ``c++11``, but this topic is out of scope in this article. If you use it, beware of your safety and sanity.

## Summary
Smart pointers allow and prevent the following issues:
* automatic deletion of memory once smart pointer is out of scope
* prevents deleting pointer more than once
* helps manage dangling pointers
* creates the concept of ownership
* eliminates cyclical dependencies
* promotes code safety

Smart pointers are a wrapper around raw pointers, which comes with other specific member function to help with development. Always lean towards using smart pointers instead of raw pointer. Smart pointers give you better control and prevents common errors in your codebase!

### Smart Pointer Summary -- Table (UncomplicatingTech, 2025)
The following table helps with the overall summary of smart pointers. Ownership and count reference should be stressed here for some key characteristics for smart pointers.

| Pointer Type        | Ownership | Reference Count | Auto Delete | Use Case(s)                              | Notes |
|:--------------------|:----------|:----------------|:------------|:-----------------------------------------| :------------ |
| ``std::unique_ptr`` | yes       | 1               | yes         | exclusive ownership                      | copies forbidden; moves allowed |
| ``std::shared_ptr`` | yes       | shared count    | yes         | multiple owners                          | |
| ``std::weak_ptr``   | no        | none            | no          | avoiding circular references, non-owning | |

## Cited Resources 
Wikipedia contributors. (2025, November 21). Smart pointer. Wikipedia. https://en.wikipedia.org/wiki/Smart_pointer

Kellett, S. (2025, November 4). Memory leak example: Understanding the nineteen types. Software Verify. https://www.softwareverify.com/blog/the-nineteen-types-of-memory-leak/

Wikipedia contributors. (2026, May 24). Memory leak. Wikipedia. https://en.wikipedia.org/wiki/Memory_leak

GeeksforGeeks. (2025, July 23). unique_ptr in C++. GeeksforGeeks. https://www.geeksforgeeks.org/cpp/unique_ptr-in-cpp/

UncomplicatingTech. (2025, April 2). Weak pointers made simple: Avoid shared pointer traps in C++ [Video]. YouTube. https://www.youtube.com/watch?v=xHS6YVttLJ0