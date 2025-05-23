<!-- #summary Examples to complete keys from a prefix. -->

# How to Complete

## Completion Dictionary Construction

The following example gives a function to build a completion dictionary from a pair of simple dawg and basic dictionary.
The function uses *dawgdic::Guide* and *dawgdic::GuideBuilder* for completing keys in dictionary order.
Instead, you can use the combination of *dawgdic::RankedGuide* and *dawgdic::RankedGuideBuilder* for completing keys in descending record order.

```cpp
#include <iostream>

#include <dawgdic/guide-builder.h>

// Builds a completion dictionary from a pair of simple dawg "dawg" and
// dictionary "dic". Then, writes the completion dictionary into
// the given output stream "guide_output".
bool BuildGuide(const dawgdic::Dawg &dawg, const dawgdic::Dictionary &dic,
  std::ostream *guide_output) {

  // Builds a completion dictionary "guide".
  dawgdic::Guide guide;
  if (!dawgdic::GuideBuilder::Build(dawg, dic, &guide))
    return false;

  // Writes the completion dictionary into the given output stream.
  return guide.Write(guide_output);
}
```

## Key Completion

The following example gives a function to find keys
which start with a specified string.
The function uses the combination of base dictionary and
completion dictionary to provide quick key completion.
Similar to the above example, you can use the combination of
*dawgdic::RankedGuide* and *dawgdic::RankedCompleter*
for completing keys in descending record order.

```cpp
#include <iostream>

#include <dawgdic/completer.h>

// Finds keys which start with a given string "prefix" by using the
// combination of a base dictionary "dic" and a completion dictionary
// "guide". Then, only first "top_n" keys are written to the standard
// output stream.
void Complete(const dawgdic::Dictionary &dic, const dawgdic::Guide &guide,
  const char *prefix, int top_n) {

  // Checks if the dictionary contains keys starting with a given
  // string "prefix". At the same time, the start position of
  // key completion is acquired.
  dawgdic::BaseType index = dic.root();
  if (!dic.Follow(prefix, &index))
    return;

  // Initializes an object for key completion.
  dawgdic::Completer completer(dic, guide);

  // Prepares for key completion.
  completer.Start(index, prefix);

  for (int i = 0; i < top_n; ++i) {

    // Completes the next key.
    // Finishes the function if there are no more keys.
    if (!completer.Next())
      break;

    // Shows a completed key and its associated record.
    std::cout << completer.key() << ": " << completer.value() << std::endl;
  }
}
```
