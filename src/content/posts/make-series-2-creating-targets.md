---
title: 'Creating and Linking Targets'
published: 2026-01-07
draft: false
description: 'CMake has the ability to create targets. This article shows how to create a library and link targets (a library) to an executable.'
series: 'CMake Tutorial'
tags: ['cmake']
---

# Article Goals
In this article, we will look into more development topics of CMake and examine the CMake language in depth. We will take a look at the CMake documentation, and understand some key concepts which includes targets for other objects to use and consume them. We will discover how to create them, use them, and add properties to targets. For example, targets have scope information, and the scope of these targets effects how other targets consume and use them. 

Then we will look into a real example with CMake using a command line program.

# Article's Scope
We will look into the some key concepts:
* dig deeper into the the three basic commands a ***CMakeLists.txt*** must contain by looking into the documentation.
* creating targets (a library) from an executable to consume and use it in our program
* how different scopes (public, private, and interface) keywords effect the library during the build
* how to link the library to the main program
* see how a dependencies graphs aids in target/dependency development
* how to build just a single target, if you have many targets
* see an example program with a created library and see it run

:::note
In the previous article -- [Setting Up and Running CMake](https://kcruey.com/posts/cmake-series-1-setting-up-cmake/) -- we eluted to using multiple **CMakeLists.txt** to decrease code bloat and make it more readable for the developer. We will examine that paradigm by creating independent library in their own directory.
:::

## Prerequisites  
Getting familiar to [Article #1](https://kcruey.com/posts/cmake-series-1-setting-up-cmake/) helps. Give it a look if you haven't already.

## CMake Minimum Requirements
We will look into the three basic commands that are necessary for the root ***CMakelists.txt*** to contain. These commands are needed somewhere in the project, however they can be somewhere within the cascading subdirectories of **CMakeLists.txt**. We will see that "add_executable" command will be in another **CMakeLists.txt** in the tutorials example.

Now what are the minimum commands for CMake? In the last [article](https://kcruey.com/posts/cmake-series-1-setting-up-cmake/), we saw them in the simple executable example. We're going to reexamine them more in-depth.

Lets summarize what these command are supposed to do at a high level then look at the documentation.  
* [cmake_minimum_required](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html) -- allows the project to establish what CMake VERSION the script depends on.
* [project](https://cmake.org/cmake/help/latest/command/project.html#command:project) -- names the CMake project and allows to reference the project.
* [add_executable](https://cmake.org/cmake/help/latest/command/add_executable.html#command:add_executable) -- names the executable and allows to reference the executable.

### Reading CMake Documentation
CMake conveniently documentation gives us the ability to look up what these commands need as inputs and elaborates what the overall command does. As a new CMake developer, it can be confusing what everything means; however for newer CMake users, the generalized command example guides us how to use the given command properly.

When reading the example commands, notice the punctuation and syntax of the command. The punctuation marks between the parentheses ("()") provides all the inputs needed but some of them can be optional. For example, anything that's capitalize must be written as-is, because it sets some CMake variables internally. Other than the capitalization, the less than and greater than ("<>") symbols requires a user input value to be as a parameter. Anything with square brackets ("[]") is considered optional with everything in between the brackets to be written as as-is and replace any "<>" variables, of course.

Lets take a look at specifically to the three basic CMake command's documentation.

#### cmake_minimum_required()
The example commands from the cmake_minimum_required [documentation](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html): 
```shell title="cmake_minimum_required() Command Example"
$ cmake_minimum_required(VERSION <min>[...<policy_max>] [FATAL_ERROR])
```

As we can see VERSION keyword is required with an input (<min\>) and everything else between the square brackets as optional. Just like previously stated, inside the square bracket must be exactly followed. With the particular option <policy_max>, gives us a range for the minimum and maximum value for the CMake version or it will throw an error.

Lets give an example.

To figure out the version of the current CMake you're one, open up your favorite command line and type in "cmake --version". For example, my version happens to be version 4.2. 

![CMake Version](../post-pictures/cmake-series/2-creating-targets/cmake-version.png) 

Lets add the FATAL_ERROR option as well, so if your CMake's version is less than 4.3, CMake will stop the configuration/generation/build stage and thrown an error to the command line. 

To illustrate this error, lets set up a file structure something like this, exactly like [article #1](https://kcruey.com/posts/cmake-series-1-setting-up-cmake/).

![Error File Structure](../post-pictures/cmake-series/2-creating-targets/cmake-min-required-error-file-structure.png)

Add the contents to the files below:

```c++ title="CMakeLists.txt"
cmake_minimum_required(VERSION 4.3 FATAL_ERROR) // will throw an error
project(Tutorial)
add_executable(hello hello-world.cpp)
```

```c++ title="hello-world.cpp"
#include <cstdio>
#include <cstdlib> // EXIT_SUCCESS

int main()
{
  std::printf("Hello World\n");
  return EXIT_SUCCESS;
}
```

Now, lets put the command as follows:

```shell title="CMake Version Error"
$ cmake -G "MinGW Makefiles" -B build -S .
```

Just like that, CMake will yell at you as expected!
![Error](../post-pictures/cmake-series/2-creating-targets/cmake-min-required-error.png)

Change back the version number to 4.2 for the error to cease.

#### project()
The example commands from the project [documentation](https://cmake.org/cmake/help/latest/command/project.html#command:project): 
```shell title="project() Command Examples"
# option #1
$ project(<PROJECT-NAME> [<language-name>...])

# option #2
$ project(<PROJECT-NAME>
        [VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
        [COMPAT_VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
        [SPDX_LICENSE <license-string>]
        [DESCRIPTION <description-string>]
        [HOMEPAGE_URL <url-string>]
        [LANGUAGES <language-name>...])
```
As we can see, there's two different forms of the acceptable project() commands. Option #1 is more compact and less complex than then option #2, and depending on the complexity of the CMake script, adding all of the optional values might be needed. However for this example, that isn't necessary, since we don't need to add more details to the project. 

We will use the simple version, and in the documentation, add the "CXX" as the language to add more detail to our project. Adding this detail acknowledges CMake to look for compilers and/or toolchains for the C++ language. Once again, this is optional but it's good to have these details in the project.

Now, modify the ***CMakeLists.txt*** as shown:

```c++ title="CMakeLists.txt"
cmake_minimum_required(VERSION 4.2 FATAL_ERROR)
project(Tutorial CXX)
add_executable(hello hello-world.cpp)
```

#### add_executable()
The example commands from the add_executable [documentation](https://cmake.org/cmake/help/latest/command/add_executable.html#command:add_executable): 

```shell title="add_executable() Command Examples"
# option #1
$ add_executable(<name> [<options>...] [<sources>...]) # normal version

# option #2
$ add_executable(<name> IMPORTED [GLOBAL]) # imported version

# option #3
$ add_executable(<name> ALIAS <target>) # alias version
```

There are multiple versions used for different types of executables, however we aren't interested in option #2 or #3. However, lets introduce a brief explanation of and IMPORTED and ALIAS keywords: 
* IMPORTED allows to connect an external executables, like another development tools, into your project
* ALIAS allows to rename any executable -- simply gives it a new name

Now back to option #1, the add executable commands makes a target, and the name of the target is set by the <name\> value. The <sources\> input adds any .cpp files initially to the target, and add as many files as you want. However we will see creating a library simplifies a massive list of .cpp files. 

```c++ title="add_executable Example"
add_executable(myProgram) // example #1
add_executable(myProgram math.cpp logs.cpp) // example #2
```
Example #1 just creates a target named "myProgram". However in Example #2, an executable created by packaging math.cpp and logs.cpp into the target executable. We don't need to discretely add .h or .hpp files in the [<sources\>...] option of the command, since they are reference in the .cpp with an #include directive.


## Creating Targets
On the first look, we've seen what are the minimum command for basic CMake script. Similarly just like the add_executable command, there's  a set of commands that help developers to create targets. Targets help support abstracting away potential complexities into one entity.

Some of the most common commands creating CMake targets and generally, they have very similar documentation syntax:
* [add_executable](https://cmake.org/cmake/help/latest/command/add_executable.html)
* [add_library](https://cmake.org/cmake/help/latest/command/add_library.html)

When you make a target, it gets named, and CMake understands to create an object or, in CMake verbiage, an artifacts. CMake refers to artifacts in a more general term than libraries, headers, or objects to name a few. 

The add_executable and add_library commands create a target, which is a key concept in CMake development.

### Adding Files to Targets
However, just because you make a target doesn't mean anything is associated with it -- assuming you didn't use the <sources\> option in the add_executable command. Think of targets as an empty vessel or shell, and  "target_source" (and similar commands) weave together translation units (cpp files) into the artifact(s).

Some commands add translation units (cpp files) to targets and [target_sources](https://cmake.org/cmake/help/latest/command/target_sources.html) is one of them.

Like we've seen in #### add_executable() section, Example #1 creates an empty target with no source files. Using the target_sources command, it effectively comparable to Example #2.
```c++ title="Similar to Example #2 in #### add_executable() Section But Using target_sources Command"
add_executable(myProgram)
target_sources(myProgram PRIVATE math.cpp logs.cpp)
```
As we see, there's a scoping keyword (PRIVATE) used. Let's take a look at what that means.

## Target Visibility
Introducing targets means now we're introducing the topic of scoping targets. Target scoping determines what's visibility relationships between the source target and other target consumers. In other words, it's a mechanism to help with managing dependencies.

Think of scoping similar functionality to C++ class scopes -- like how it defines how other targets will "see" the relationships between two targets.

However unlike C++ class scoping, CMake target visibility help pass characteristics to other targets or just reading the given target's characteristics. These command help propagates information between the two targets. This is when a relationship is created between targets.

There are three different target visibility keywords:
- Private -- will not propagate its dependencies to other targets it consumes
- Interface -- will only propagate as a dependency to other targets consuming it
- Public -- will propagate as a dependency to any other targets consuming it

Scoping is more of a complicated topic, as there isn't any correct answer when to use one over the other. After some experience, hiding target information aids better development.

To help with property propagation understanding, following table assist what keyword to scope your targets:

| Target |  Others   |         |
|:------:|:---------:|:-------:|
|        |    YES    |   NO    |
|  YES   |  PUBLIC   | PRIVATE |
|   NO   | INTERFACE |    -    |

You can this of it as -- PRIVATE is for me, INTERFACE is for others, PUBLIC is for all of us.

### Linking Targets to Each Other
Now we have an source filled artifact floating in CMake space, we now need to link that library to something -- in our case -- an executable to consume. This is when target_link_libraries command comes to the rescue. 

* [target_link_libraries](https://cmake.org/cmake/help/latest/command/target_link_libraries.html) -- connects and links specific target together and their relationship is related to the scope keyword used

```c++ title="Linking Targets Example"
add_executable(myProgram math.cpp logs.cpp) // executable created
add_library(myLib PRIVATE scientific-math.cpp) // library created

// connecting myLib library to executable as a dependency
target_link_library(myProgram PRIVATE myLib) 
```

We can see the library we created is now being consumed by the executables, so any library functions in myLib can be used by myProgram. 

## Visualization of Dependencies
Now suppose we are given a project with a lot of targets created. There's a giant web of dependencies with targets. It might not be obvious or clear how everything links together, however CMake has a tool called Graphviz to help with documentation.

For creating a dependency graph for better documentation is easier then ever. I would suggest generate the documentation inside the build directory, so you don't mixup source code and build artifacts. 

In order to use the graphviz feature, we have to create a special configuration file called "CMakeGraphVizOptions.cmake". Inside the fill add the following:
```cmake title="CMakeGraphVizOptions.cmake"
set(GRAPHVIZ_CUSTOM_TARGET TRUE)
```

Now lets start generating, use the commands to product a Graphviz dependency graph:

```shell title="Creating Dependency Graph"
$ cd build
$ cmake --graphviz=<name-of-file>.dot ..
```

## Resources
[Modern CMake for C++](https://www.packtpub.com/en-us/product/modern-cmake-for-c-9781805121800?pv2=eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJjIjoiVVNEIiwiZXhwIjoxNzY2NTk3OTU0LCJtIjoiMTM5OTcyMjEiLCJvIjoiVVMtOTc4MTgwNTEyMTgwMC1QQVBFUkJBQ0siLCJwIjo0My45OTAwMDAwMDAwMDAwMDJ9.2t3-UUfK1gsdqTYc1dTwATUHrd3XnAzC0E0Oz7hpqqk_1ixvphXaktbPvBd0k_1S0ZFRWUq6SVi-UVUgtodEEg&utm_source=google&utm_medium=cpc&utm_campaign=23082907872&puci=CjwKCAiAu67KBhAkEiwAY0jAlQFYlbzeQ2Rojn31YAnKRNnDvEdq4en7qyc9zsA6cb6cK18WA-WtYxoCx3gQAvD_BwE&gad_source=1&gad_campaignid=23088837035&gbraid=0AAAAAqt_OJ2zJY6kfcp8FvvGFZXNtftPI&gclid=CjwKCAiAu67KBhAkEiwAY0jAlQFYlbzeQ2Rojn31YAnKRNnDvEdq4en7qyc9zsA6cb6cK18WA-WtYxoCx3gQAvD_BwE) by Rafał Świdziński
