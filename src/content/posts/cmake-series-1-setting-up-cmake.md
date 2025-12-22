---
title: 'Setting Up CMake'
published: 2025-12-23
draft: false
description: 'Setting up your system for CMake development and creating a simple executable.'
series: 'CMake Tutorial'
tags: ['cmake', 'chocolatey', 'homebrew']
---

# CMake Tutorial Series
For this multipart series, we will look into [CMake](https://cmake.org/) development tool by [Kitware](https://www.kitware.com/). CMake is an open source build generator tool useful for any C/C++ developer who wants to control their native and cross-platform builds. CMake doesn't itself build executables but calls other tools to create them.

## Series Goals
We will be peering into the inners of CMake build system, we will examine some key and important CMake features including CMake, CTest, and CPack.

* ***CMake*** is a cross-platform build and configuration tool that links compilers and build generators together by producing executables for different operating systems (OS).
* ***CTest*** is a test runner/driver that initiates project's tests, static/dynamic analyzers, documentation, reports, and more.
* ***CPack*** is a packaging tool that helps deploy installers and repackage source code onto the target's device.

:::note
Don't get confused, most of the time when referring to CMake, I mean the build tool and not the software itself.
:::

## Prerequisites  
For this series, some required development tools are needed to be installed on your computer. Depending on the OS, follow the installation for Windows, MacOS, or Linux. However since there's so many different Linux distributions, some research on your end is required on how to install the packages... so, you're on your own for this one!

To follow along with this series, we need to add the follow dependencies on your computer:
* cmake (build, testing, and packaging tool -- the star of this series)
* make (build tool)
* gcc/g++ (C/C++ compilers)
* tree (CLI tool that prints files)

However depending on what OS (Widows or MacOS) you're on, different installation process will occur. Refer to your OS's directions below.

### Installing on Windows
If you're on Widows, I would suggest downloading a package manager like [Chocolatey](https://chocolatey.org/install). Then follow the instructions provided by Chocolatey and make sure you install it for "individual use".

Once downloaded, add make (build tool), cmake (build, testings, and packaging tool), and gcc/g++ (compilers). Open up your favorite command line interface (CLI) and type the following:
```shell title="Chocolatey CLI Commands"
$ choco install make cmake mingw tree -y
```

:::important 
Remember on a Window's machine, most likely you would need to go into 'administration mode' in order to install these packages on the system. Run the command prompt in 'administration mode' to continue installation.
:::

### Installing on MacOS
If you're on MacOS, I would suggest installing [Homebrew](https://brew.sh/) as your package manager. Follow the instructions on Homebrew website for installation.

Once downloaded, add make (build tool), cmake (build, testings, and packaging tool), and gcc/g++ (compilers). Open up your favorite command line interface (CLI) and type the following:
``` shell title="Homebrew CLI Commands"
$ brew install make cmake gcc g++ tree -y
```

### Installing on Linux
Depending on what distro your on, use that specific distro's package manager and install cmake, make, and gcc/g++. Good luck! You're on your own on this one. I believe in you! :laughing:

## CMake Basics
Before we get into the details of CMake, CMake works by identifying a file called ***CMakeLists.txt*** and parses commands inside that file. It must be exactly named that or CMake won't understand what to do.

Always put a ***CMakeLists.txt*** at the base of your directory and this particular ***CMakeLists.txt*** is referred as the root ***CMakeLists.txt***. This is by far the most important ***CMakeLists.txt*** in your project, and think of it like the base of a tree. Everything else grows from the root.

However if your project is getting complicated, it isn't uncommon to add supplementary ***CMakeLists.txt*** in other subdirectories. We will eventually investigate into this paradigm later in the series. It helps your CMake builds look cleaner and minimizes a monolithic root ***CMakeLists.txt*** and eventually minimize debugging efforts.

## How Does CMake Produce an Executable?
Remember, CMake is a build generator, which means it calls other native tools on your computer to create an executable. That's why we need to install dependencies from previous sections above. CMake must acknowledge the other tools on your system, or CMake will throw an error.

Luckily, only three stages that must occur before an output is produced. These stages are ***configuration***, ***generation***, and ***build*** stages.
![CMake's Three Stages](../post-diagrams/cmake-stages.png)

CMake must pass these stages in order; however for the purpose development, you can combine the configuration and generation stages in one CLI command using [flags/options](https://cmake.org/cmake/help/latest/manual/cmake.1.html#options). In CMake, flags/options are signified by a hyphen "-" followed by a capital letter or a dash "--" followed by a word. Think of flags/options as adding more details to CMake to config, generate, and build projects.

## CMake Code
Now it's time to start coding!

Anywhere on your computer, add a directory called "cmake-tutorial" and inside this director, add "hello-world.cpp" and "CMakeLists.txt" files.

"hello-world.cpp" is a small program that just print "Hello World" to the command line and CMakeLists.txt is the script that builds the program.
```c++ title="hello-world.cpp"
#include <cstdio>
#include <cstdlib> // EXIT_SUCCESS

int main()
{
  std::printf("Hello World\n");
  return EXIT_SUCCESS;
}
```

```c++ title="CMakeLists.txt"
cmake_minimum_required(VERSION 4.2)

project(Tutorial)

add_executable(hello hello-world.cpp)
```
:::note
For CMakeLists.txt, these three commands -- cmake_minimum_required, project, and add_executable -- are the minimum commands needed to build a CMake project.
:::


Once completed, your folder should look something like this: 
![tutorial directory](../post-pictures/cmake-series/setting-up-cmake-1.png)

Finally with everything in place, lets get configuring!

## Configuration Stage
Lets examine the configuration stage!
![CMake Configuration Stage](../post-diagrams/cmake-stages-red-config.png)

During the Configuration stage, CMake reads the ***CMakeLists.txt*** and checks to see what dependencies, compilers, linkers, paths, and system information are known to CMake. Basically this stage names and sets what CMake should be working with, and sets the environment/cache variables for future actions.

In order to acknowledge these steps to CMake, go to the directory where the "hello-world.cpp" and "CMakeLists.txt" and type in the following:

```shell title="Configuration Stage Commands"
$ cmake -B build -S .
```
:::note
CMake prepares the source directory (using the -S flag) and build directory (using the -B flag) for the generation stage. 

The -B flag tells CMake where the location of the build tree directory, and if there isn't a directory called "build", one will be automatically created.

The -S flag tells CMake where the location of the source tree or source code will be located. This directory or location must be defined. In this case, the "." means the current directory.
:::

Oops, it seems like something went wrong. 

![generation error](../post-pictures/cmake-series/config-error.png)

Before the error was thrown, it seems like it was trying to build for "NMake Makefiles" on line 3. CMake is telling us that we're generating the build using wrong type. Previously, we had to download MinG. CMake will be using that generator, but it's not. Now let's configure CMake for the "MinGW Makefiles" instead of "NMake Makefiles".

## Generation Stage
Lets examine the generation stage!
![CMake Generation Stage](../post-diagrams/cmake-stages-red-generate.png)

CMake has the ability to use different generation systems. To see all the possible generation types, execute the following command:
```shell
$ cmake --help 
```
Now scroll down all the way to the bottom, the command will list out all the possible generators. Pick one that best suits you depending on your operating system. However we did install MinGW. I would suggest "MinGW Makefiles".

:::note
You will see a "*" on the left side of the generator's name. That's the default and we need to change it.

Currently, my setup was generating for "NMake Makefiles" so that's why we saw the error above because we don't have these dependencies.
:::

Let's delete the build folder, and use this new command to directly state what generator that should be used.
```shell title="Configuration Stage Commands"
$ rm -fr build
$ cmake -G "MinGW Makefiles" -B build -S .
```

:::note
Notice that -G flag/option from the command above. This tells CMake what type of generator shall be used.
:::

Upon linking the generator flag/option (-G), the configuration and generation stages should produce a successful build.

![Successful Config and Generation Step](../post-pictures/cmake-series/successful-generation.png)

After applying the new generator flag/option, you can see that CMake automatically found the C and C++ compilers on the system via downloading MinGW. It also took the Configuration stage 1.4 seconds and the Generation stage 0.0 seconds, confirming the configuration and generation stages are complete.

Now finally, we have an output inside the build directory. 

Take a look in the "build" folder that CMake auto-created during these steps. Just take a mental note that CMake DOES produce something during the configuration and generation stages.

### Generator Stage (Small) Optimization
We've seen that you need to add a -G flag to the command line every time during the configuration and generation stages. It can be annoying to type this out every single time. However, there is a small trick to speed up the process.

Open up your command line and type in the following:
```shell
$ export CMAKE_GENERATOR="MinGW Makefiles"
```

What this does is it creates a CLI variable on your system to understand that it needs to generate build for "MinGW Makefiles". 

To verify you set the CLI export variable, type 'export' into the CLI and scroll down.

```shell
$ export
```

You should see this somewhere in the wall of text:
![Export Variable](../post-pictures/cmake-series/export-confirmation.png)

Now lets configure the project again. Delete the build folder again, and run the command below. You should see it will configure and generate the files successfully.

```shell title="Simpler Configuration Stage Commands"
$ rm -fr build
$ cmake -B build -S .
```

Once again, the configuration and generation step should build without errors!

:::warning
However there's pitfall with this, if you exit your CLI, the export variable (CMAKE_GENERATOR) gets erased, so it's only temporary. Try it out, close your CLI and type in the standard "cmake -B build -S .". You will see it throws an error again.
:::

## Building Stage
Lets examine the build stage!
![CMake Building Stage](../post-diagrams/cmake-stages-red-build.png)

The build stage allows CMake to create build target artifacts, which include executables, libraries, and so much more. However in our case, we will only create a simple executable.

Go to the directory with the "hello-world.cpp" and "CMakeLists.txt" file, and execute the following commands:
```shell
$ rm -fr build # deleting build folder
$ cmake -G "MinGW Makefiles" -B build -S . # config/generation stage
$ cmake --build build
```

![Creating an Executable](../post-pictures/cmake-series/build-stage.png)

After the command successfully builds, now open the "build" directory on your system and file the executable. Now run it.

```shell
$ cd build # move to build directory
$ ./hello.exe
```

Congratulations, you built your first executable using CMake!

## Resources
[Modern CMake for C++](https://www.packtpub.com/en-us/product/modern-cmake-for-c-9781805121800?pv2=eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJjIjoiVVNEIiwiZXhwIjoxNzY2NTk3OTU0LCJtIjoiMTM5OTcyMjEiLCJvIjoiVVMtOTc4MTgwNTEyMTgwMC1QQVBFUkJBQ0siLCJwIjo0My45OTAwMDAwMDAwMDAwMDJ9.2t3-UUfK1gsdqTYc1dTwATUHrd3XnAzC0E0Oz7hpqqk_1ixvphXaktbPvBd0k_1S0ZFRWUq6SVi-UVUgtodEEg&utm_source=google&utm_medium=cpc&utm_campaign=23082907872&puci=CjwKCAiAu67KBhAkEiwAY0jAlQFYlbzeQ2Rojn31YAnKRNnDvEdq4en7qyc9zsA6cb6cK18WA-WtYxoCx3gQAvD_BwE&gad_source=1&gad_campaignid=23088837035&gbraid=0AAAAAqt_OJ2zJY6kfcp8FvvGFZXNtftPI&gclid=CjwKCAiAu67KBhAkEiwAY0jAlQFYlbzeQ2Rojn31YAnKRNnDvEdq4en7qyc9zsA6cb6cK18WA-WtYxoCx3gQAvD_BwE) by Rafał Świdziński