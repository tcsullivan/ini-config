# ini-config

A single-header library that converts INI-formatted string literals to a key-value pair list at compile-time.

Requires C++20; tested on gcc 10.1 and clang trunk. Passes `-Wall -Wextra -pedantic`.

## Features
 * Direct accesses to values are compile-time evaluated, allowing an INI config to be used for project/program configuration.
 * Values can be accessed as strings, integers, or floating-point numbers.
 * Run-time support includes key lookup, iteration through the key-value list, and key existance checking; all of which can be filtered by section.

[Try it on Godbolt.](https://godbolt.org/z/Ys1o9G)

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

// Or, go for a more functional look:
//constexpr auto config = make_ini_config<R"( ... )">;

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

