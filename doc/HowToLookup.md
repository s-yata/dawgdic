<!-- #summary Examples to lookup keys in a dictionary. -->

# How to Lookup

## Exact Matching

The following example gives a function to lookup a key.
The function lookups a given key and writes the result into the standard output stream.

```cpp
#include <dawgdic/dictionary.h>

// Lookups a key in a dictionary.
void Lookup(const dawgdic::Dictionary &dic, const char *key) {

  // Checks whether the dictionary contains the given key or not.
  if (dic.Contains(key))
    std::cout << key << ": " << ": found" << std::endl;
  else
    std::cout << key << ": " << ": not found" << std::endl;

  // Checks whether the dictionary contains the given key or not.
  // If the key is available, then reads a record from the dictionary.
  int record;
  if (dic.Find(key, &record))
    std::cout << key << ": " << ": found (" << record << ')' << std::endl;
  else
    std::cout << key << ": " << ": not found" << std::endl;
}
```

## Prefix Matching

The following example gives a function to find all the occurrences of keys in text. The function finds keys and writes the results into the standard output stream.

```cpp
#include <string>

#include <dawgdic/dictionary.h>
#include <dawgdic/dictionary-explorer.h>

// Finds all the occurrences of keys in a given text.
void Filter(const dawgdic::Dictionary &dic, const char *text) {

  for (const char *p = text; *p != '\0'; ++p) {

    // Begins prefix matching at each position in a text.
    BaseType index = dic.root();
    for (const char *q = p; *q != '\0'; ++q) {

      // Follows a transition.
      if (!dic.Follow(*q, &index))
        break;

      // Shows information of a matched key.
      // The information consists of the start position, the length,
      // the record and the string of that key.
      if (dic.has_value(index)) {
        std::cout << '(' << (p - text) << ", " << (q + 1 - p) << ", "
                  << dic.value(index) << "): ";
        std::cout.write(p, q + 1 - p) << std::endl;
      }
    }
  }
}
```
