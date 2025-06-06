<!-- #summary This document provides class interfaces. -->

# Interfaces of DAWG Dictionary Library

## Main Interfaces

The following table shows the main classes of the library *dawgdic*.

| Header | Namespace | Class |
|--|--|--|
| dawgdic/dawg.h | dawgdic | Dawg |
| dawgdic/dawg-builder.h | dawgdic | DawgBuilder |
| dawgdic/dictionary.h | dawgdic | Dictionary |
| dawgdic/dictionary-builder.h | dawgdic | DictionaryBuilder |
| dawgdic/guide.h | dawgdic | Guide |
| dawgdic/guide-builder.h | dawgdic | GuideBuilder |
| dawgdic/completer.h | dawgdic | Completer |
| dawgdic/ranked-guide.h | dawgdic | RankedGuide |
| dawgdic/ranked-guide-builder.h | dawgdic | RankedGuideBuilder |
| dawgdic/ranked-completer.h | dawgdic | RankedCompleter |

----

## Dawg

| Header | Namespace |
|--|--|
| dawgdic/dawg.h | dawgdic |

```cpp
// Returns the number of allocated units.
SizeType size() const;
```

In fact, each unit is associated with each transition of a dawg, and the number of allocated units is almost same as the number of transitions.

```cpp
// Returns the number of transitions.
SizeType num_of_transitions() const;
// Returns the number of states.
SizeType num_of_states() const;
```

```cpp
// Returns the number of transitions merged in dawg construction.
SizeType num_of_merged_transitions() const;
// Returns the number of states merged in dawg construction.
SizeType num_of_merged_states() const;
```

----

## DawgBuilder

| Header | Namespace |
|--|--|
| dawgdic/dawg-builder.h | dawgdic |

```cpp
// Work same as the functions of dawgdic::Dawg.
SizeType size() const;
SizeType num_of_transitions() const;
SizeType num_of_states() const;
SizeType num_of_merged_transitions() const;
SizeType num_of_merged_states() const;
```

```cpp
// Inserts a key into a dawg.
bool Insert(const CharType *key, ValueType value = 0);
bool Insert(const CharType *key, SizeType length, ValueType value);
```

Keys must be inserted in dictionary order.
If the order is incorrect, these functions return false.
Also, if the *length* of key is specified and the key contains a null character, these functions return false.
On the other hand, if *length* is unspecified, the function uses the number of bytes until a null character instead of *length*.
A record *value* must be 0 or a positive integer.

```cpp
// Finishes building a dawg and initializes this object.
bool Finish(Dawg *dawg);
```

This function must be called after inserting all the keys.
In this function, *this* object passes own managed member objects to a given *dawg*.
Therefore, this function also initializes *this* object to avoid unexpected errors.

----

## Dictionary

| Header | Namespace |
|--|--|
| dawgdic/dictionary.h | dawgdic |

```cpp
// Returns the start address of the core array.
const DictionaryUnit *units() const;
```

Actually, a dictionary is represented by just one array.
This means that a dictionary is restored from the array returned by this function.
The functions *Map()* are provided for this purpose.

```cpp
// Returns the number of units in the core array.
SizeType size() const;
// Returns the required number of bytes for the core array.
SizeType total_size() const;
// Returns the number of bytes written to a stream.
SizeType file_size() const;
```

```cpp
// Reads a dictionary from an input stream.
bool Read(std::istream *input);
// Writes a dictionary into an output stream.
bool Write(std::ostream *output) const;
```

```cpp
// Check whether a key exists in dictionary or not.
bool Contains(const CharType *key) const;
bool Contains(const CharType *key, SizeType length) const;
```

```cpp
// Check whether a key exists in dictionary ot not, and if so,
// return its associated record.
ValueType Find(const CharType *key) const;
ValueType Find(const CharType *key, SizeType length) const;
bool Find(const CharType *key, ValueType *value) const;
bool Find(const CharType *key, SizeType length, ValueType *value) const;
```

If a given keys does not exist in dictionary, these function return -1 as a record *value*.

```cpp
// Returns the index of the initial state.
BaseType root() const;
// Returns whether a given state corresponds to the end of a key.
bool has_value(BaseType index) const;
// Returns a record associated with a given state.
ValueType value(BaseType index) const;
// Follows transitions and modifies a given index.
bool Follow(CharType label, BaseType *index) const;
bool Follow(const CharType *s, BaseType *index);
bool Follow(const CharType *s, BaseType *index, SizeType *count);
bool Follow(const CharType *s, SizeType length, BaseType *index);
bool Follow(const CharType *s, SizeType length, BaseType *index, SizeType *count);
```

These functions are provided for prefix matching.
First, an index must be initialized by using the function *root()*.
Then, the functions *Follow()* follow transitions with a given *label* or labels in a given string *s*.
After that, the function *has_value()* returns whether the current state corresponds to the end of a key.
And if the function *has_value()* returns true, the function *value()* returns a record associated with that key.

```cpp
// Uses arbitrary memory space as the core array.
void Map(const void *address);
void Map(const void *address, SizeType size);
```

If the 2nd argument *size* is omitted, the first 4 bytes of given space are used as the buffer size.

----

## DictionaryBuilder

| *Header* | dawgdic/dictionary-builder.h |
|--|--|
| *Namespace* | dawgdic |

```cpp
// Builds a dictionary from a dawg.
static bool Build(const Dawg &dawg, Dictionary *dic, BaseType *num_of_unused_units = NULL);
```

This function builds a dictionary from a *dawg* and updates *dic*.
If the 3rd argument *num_of_unused_units* is not NULL, this function assigns the number of empty elements in the double-array to this argument *num_of_unused_units*.

----

## Guide

| Header | Namespace |
|--|--|
| dawgdic/guide.h | dawgdic |

```cpp
// Reads a completion dictionary from an input stream.
bool Read(std::istream *input);
// Writes a dictionry into an output stream.
bool Write(std::ostream *output) const;
```

----

## GuideBuilder

| Header | Namespace |
|--|--|
| dawgdic/guide-builder.h | dawgdic |

```cpp
// Builds a completion dictionary from a dawg and a dictionary.
static bool Build(const Dawg &dawg, const Dictionary &dic, Guide *guide);
```

This function builds a completion dictionary from a pair of *dawg* and *dic*, and then updates *guide*.

----

## Completer

| Header | Namespace |
|--|--|
| dawgdic/completer.h | dawgdic |

```cpp
// Initializes an object.
Completer();
// Initializes an object and sets required dictionaries.
Completer(const Dictionary &dic, const Guide &guide);
```

```cpp
// Sets a base dictionary.
void set_dic(const Dictionary &dic);
// Sets a completion dictionary.
void set_guide(const Guide &guide);
```

```cpp
// Gets a reference to base dictionary.
const Dictionary &dic() const;
// Gets a reference to completion dictionary.
const Guide &guide() const;
```

```cpp
// Prepares for key completion.
void Start(BaseType index, const char *prefix = "");
void Start(BaseType index, const char *prefix, SizeType length);
```

These functions prepare for completing keys from the given start position *index*.
The 2nd argument *prefix* does not affect the start position, and it is just attached to completed keys as the shared prefix.
By passing the sequence of character from the root to the start position, the following function *Next()* builds perfect keys.

```cpp
// Completes the next key.
bool Next();
// Returns the completed key as a zero terminated string.
const char *key() const;
// Returns the length of the completed key.
SizeType length() const;
// Returns the record associated with the completed key.
ValueType value() const;
```

----

## RankedGuide

| Header | Namespace |
|--|--|
| dawgdic/ranked-guide.h | dawgdic |

See *Guide*.

----

## RankedGuideBuilder

| Header | Namespace |
|--|--|
| dawgdic/ranked-guide-builder.h | dawgdic |

See *GuideBuilder*.

----

## RankedCompleter

| Header | Namespace |
|--|--|
| dawgdic/ranked-completer.h | dawgdic |

See *Completer*.
