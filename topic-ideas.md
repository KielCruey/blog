# Ideas
* ccmake and cmake-gui
    - other ways to visualize cmake via different GUIs
* general cmake language topics
    - variables
    - loops
    - if statements
* cmake -P is called "script mode", it informs CMake this file is not intended to have a project() command. We're not building any software, instead using CMake only as a command interpreter.
    * create a script
* cmake macros and functions
    * how do use them
* find_packages command
    - add other code/libraries and use them in your project





## Using CMake to Build Just a Target
```shell title="Building a Specific Targets Command"
$ cmake --build build --target <target-name>
```







# Target Stuff
## Setting Properties to Targets
setting properties to targets use the following command: 
* [set_property](https://cmake.org/cmake/help/latest/command/set_property.html)(TARGET \<target> PROPERTY \<name> \<value>)
* changing the properties that already exist by using:
  * [set_property](https://cmake.org/cmake/help/latest/command/set_property.html)(PROPERTY \<name> \<value1>)
  * [get_property](https://cmake.org/cmake/help/latest/command/get_property.html)(PROPERTY \<name>)

### Adding Dependencies to Targets
* [add_dependencies](https://cmake.org/cmake/help/latest/command/add_dependencies.html)(\<target> \<dependency>)
  * tells cmake that other translation units depend on this target. Basically you can add requirements for this target, if you don't have the dependencies, CMake will scream at you!
* [get_target_property](https://cmake.org/cmake/help/latest/command/get_target_property.html)
* [set_target_property](https://cmake.org/cmake/help/latest/command/set_target_properties.html)


* [target_compile_definitions](https://cmake.org/cmake/help/latest/command/target_compile_definitions.html)(\<source> \<INTERFACE|PUBLIC|PRIVATE> [items1...]) (page 200)
  * adding a new definition by doing the following command "-Dname=definition"

For the second argument, the keywords INTERFACE, PUBLIC, AND PRIVATE are used. Depending on what the target property is, the keyword is propagated to the target.
