# ini-config

A single-header library that converts INI-formatted string literals to a key-value pair list at compile-time.

## Features
 * Direct accesses to values are compile-time evaluated, allowing an INI config to replace a list of macros or `constexpr` globals.
 * Values can be accessed as strings, integers, or floating-point numbers.
 * Run-time support: can iterate through the key-value list or check for a key's existance, both with optional filtering by section.

Requires C++20. Tested to work on gcc 10.1 and clang trunk. Passes `-Wall -Wextra -pedantic`.

[Try it on Godbolt.](https://godbolt.org/z/WTPzE3)

### INI format notes
 * Handles single-line `key=value` pairs (extra whitespace is okay)
 * Supports sections
 * Supports comments (start line with ';' or '#')
 * Supports wide strings
 * INI format is validated at compile-time, with future goal of clearly reporting syntax errors

## How to use
```cpp
#include "ini_config.hpp"

// Simply place the _ini suffix at the end of your config string:
constexpr auto config = R"(
someflag = true

[Cat]
color = gray
lives = 9
)"_ini;

auto KVPcount = config.size();            // = 3
for (auto kvp : config) {}                // Iterate through all KVPs
                                          // (or use begin()/end())
for (auto kvp : config.section("Cat")) {} // Iterate through all KVPs under [Cat] section
                                          // (or use begin("Cat")/end("Cat"))
config.get("someflag");                   // Searches entire config for "someflag", picks first match
                                          // This call gets compile-time evaluated to "true"
config.get("Cat", "lives");               // Searches "Cat" section, compile-time evaluated to "9"
config.get<int>("Cat", "lives");          // Compile-time evaluated to 9
config.get("Dog", "lives");               // Does not exist, compile-time evaluated to ""
config.contains("Dog", "lives");          // Compile-time evaluated to false

config.tryget(argv[2]);                   // Same interface and behavior as get(),
                                          // use this when run-time evaluation is necessary
config.trycontains("color");              // Run-time evaluated to true
```
See the header file for further documentation.
