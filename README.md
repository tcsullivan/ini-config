# ini-config

A single-header library that converts INI-formatted string literals to a key-value pair list at compile-time.

Requires C++20. Tested to work on gcc 10.1 and clang trunk. Passes `-Wall -Wextra -pedantic`.

[Try it on Godbolt.](https://godbolt.org/z/K8GGov)

## Features
 * Comments and whitespace removed during compilation
 * Handles single-line `key=value` pairs with very basic syntax error reporting
 * Supports sections
 * Supports comments (start line with ';' or '#', preceding whitespace is okay)
 * Supports wide strings

## How to use
```cpp
#include "ini_config.hpp"

auto config = R"(
someflag = true

[Cat]
color = gray
lives = 9
)"_ini;

// Returns count of key-value pairs (KVPs), 3 in this case
config.size();
// Iterate through all KVPs
for (auto kvp : config) {}
// Iterate through all KVPs under [Cat] section
for (auto kvp : config.section("Cat")) {}
// Access specific values
config.get("someflag");     // Returns "true"
config.get("lives");        // Searches all KVPs, returns "9"
config["lives"];            // Same as above
config.get("Cat", "color"); // Only searches [Cat] section, Returns "gray"
config.get("Dog", "color"); // No match, returns ""
```
See the header file for further documentation.

## How it works (brief)
1. `_ini` prefix constructs an `ini_config` object with the given string.
2. Syntax is validated, and a character array is sized to fit all section names, keys, and values.
3. The array is populated with values.
4. Compiled result is the minimized array, with efficient functions for iteration and access.
