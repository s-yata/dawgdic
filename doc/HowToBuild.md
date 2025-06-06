<!-- #summary Examples to build a dictionary from a sorted lexicon. -->

# How to Build

## Dictionary Construction

The following example gives a function to build a dictionary from a sorted lexicon.
This function first reads keys from a given input stream and builds a dawg.
Then, this function builds a dictionary from that dawg.
Finally, this function writes that dictionary into a given output stream.

```cpp
#include <iostream>
#include <string>

#include <dawgdic/dawg-builder.h>
#include <dawgdic/dictionary-builder.h>

// Reads sorted keys from the given input stream "lexicon_input" and
// builds a dictionary from the keys, and then writes the dictionary
// into the given output stream "dic_output".
bool BuildDictionary(std::istream *lexicon_input, std::ostream *dic_output) {

  // Keys must be sorted in dictionary order.
  // This example assumes that given keys are already sorted.
  dawgdic::DawgBuilder dawg_builder;
  std::string key;
  while (std::getline(*lexicon_input, key)) {

    // If only one argument is passed, the function Insert() handles
    // the given argument as a key which must be a zero-terminated
    // character sequence. Also, the default value 0 is given as its record.
    if (!dawg_builder.Insert(key.c_str()))
      return false;

    // Programmers can give a record as the 2nd argument.
    // if (!dawg_builder.Insert(key.c_str(), record))
    //   return false;

    // Programmers can give a length and a record as the 2nd and 3rd arguments.
    // if (!dawg_builder.Insert(key.c_str(), length, record))
    //   return false;

    // Notes:
    //   Zero-length key is not allowed.
    //   Binary key, including a null character, is not allowed.
    //   Records must be 0 or positive integers.
  }

  // Finishes building dictionary. In the function Finish(), the object
  // "dawg_builder" is automatically initialized.
  dawgdic::Dawg dawg;
  dawg_builder.Finish(&dawg);

  // Builds a dictionary from a dawg.
  dawgdic::Dictionary dic;
  if (!dawgdic::DictionaryBuilder::Build(dawg, &dic))
    return false;

  // Writes the dictionary into the given output stream.
  return dic.Write(dic_output);
}
```
