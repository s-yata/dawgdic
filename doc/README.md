<!-- #summary Please read this document first. -->
<!-- #labels Phase-Design,Featured -->

# C++ Header Library for DAWG Dictionaries

## Introduction

This project provides a library *dawgdic* for building and accessing dictionaries implemented with directed acyclic word graphs (DAWG).

A dawg is constructed by minimizing a trie as a deterministic finite automaton (DFA),
and thus the dawg has an advantage in memory usage.
In addition, *dawgdic* uses a double-array as a base data structure, so its retrieval speed is as fast as that of *Darts*, a library for building and accessing double-array tries.

- Documentation
  - [README](README.md): Please read this document first.
    - [HowToBuild](HowToBuild.md): Examples to build a dictionary from a sorted lexicon.
    - [HowToLookup](HowToLookup.md): Examples to lookup keys in a dictionary.
    - [HowToComplete](HowToComplete.md): Examples to complete keys from a given prefix.
  - [Interface](Interface.md): This document describes class interfaces.

----

## How To Install

### Basic Installation

On Unix-like systems, please follow the basic installation process below:

```cpp
./configure
make
make install
```

This process installs 2 commands, *dawgdic-build* and *dawgdic-find*,
and headers *<dawgdic/`*`.h>* as a C++ header library.
Outline of the commands and headers are given as follows:

- Commands
  - *make-dawg*
    - Builds a dictionary from a lexicon.
    - Keys must be divided by line separaters (LF).
    - Keys must be sorted in dictionary order.
  - *dawgdic*
    - Finds keys in prefixes of each input line.
- Headers
  - *<dawgdic/`*`.h>*
    - Define classes for building a dictionary from a lexicon.
    - Define classes for finding keys in a dictionary.
    - Define classes for completing keys from a given prefix.

### Without Installation

If you need to use *dawgdic* without installation, e.g., on Windows.
Please copy headers in src/dawgdic to
your project directory or include directory.

----

## How To Use

### Bulding Dictionary

The construction process is as follows:

- Dawg construction
  - Builds a simple dawg from a lexicon.
    - Keys must be inserted in dictionary order.
    - *dawgdic::DawgBuilder* is a class for building a simple dawg.
    - *dawgdic::Dawg* is a simple dawg class.
- Dawg conversion
  - Transforms a simple dawg into a dictionary.
    - *dawgdic::DictionaryBuilder* is a class for building a dictionary.
    - *dawgdic::Dictionary* is a dictionary class.

The following example builds a dictionary from a lexicon which consists of 2 keys "apple" and "orange" without records.
In fact, the default value 0 is attached to the keys.
If you need records for keys other than the default value, please pass a record to a member function *Insert()* of *dawgdic::DawgBuilder* as the 2nd or 3rd argument.

```cpp
#include <ofstream>

#include <dawgdic/dawg-builder.h>
#include <dawgdic/dictionary-builder.h>

// Inserts keys into a simple dawg.
dawgdic::DawgBuilder dawg_builder;
dawg_builder.Insert("apple");
dawg_builder.Insert("orange");

// Finishes building a simple dawg.
dawgdic::Dawg dawg;
dawg_builder.Finish(&dawg);

// Builds a dictionary from a simple dawg.
dawgdic::Dictionary dic;
dawgdic::DictionaryBuilder::Build(dawg, &dic);

// Writes a dictionary into a file "test.dic".
std::ofstream dic_file("test.dic", ios::binary);
dic.Write(&dic_file);
```

### Accessing Dictionary

The following sample code gives the way of simple dictionary lookups.
If you need to find keys in prefixes of an input string,
please use a member function *Follow()* of *dawgdic::Dictionary*.

```cpp
#include <ifstream>
#include <iostream>

#include <dawgdic/dictionary.h>

// Reads a dictionary from a file "test.dic".
std::ifstream dic_file("test.dic", ios::binary);
dawgdic::Dictionary dic;
dic.Read(&dic_file);

// Checks if a key exists or not.
if (dic.Contains("apple"))
  std::cout << "apple: found" << std::endl;

// Finds a key and gets its associated record.
if (dic.Find("banana") < 0)
  std::cout << "banana: not found" << std::endl;
```

----

## References

### Related Papers

- Aoe, J.: *An Efficient Digital Search Algorithm by Using a Double-Array Structure*.
  - _IEEE Transactions on Software Engineering_ *15*(9) (September 1989) 1066-1077
- Aoe, J., Morimoto, K., Sato, T.: *An Efficient Implementation of Trie Structures*.
  - _Software: Practice and Experience_, *22*(9) (September 1992) 695-721
- Daciuk, J., Watson, B.W., Mihov, S., Watson, R.E.: *Incremental Construction of Minimal Acyclic Finite-State Automata*.
  - _Computational Linguistics_ *26*(1) (March 2000) 3-16
- Morita, K., Fuketa, M., Yamakawa, Y. and Aoe, J.: *Fast insertion methods of a double-array structure*.
  - _Software: Practice and Experience_ *31*(1) (January 2001) 43-65
- Ciura, M.G., Deorowicz, S.: *How to squeeze a lexicon*.
  - _Software: Practice and Experience_ *31*(11) (September 2001) 1077-1090
- Yata, S., Oono, M., Morita, K., Fuketa, M., Sumitomo, T. and Aoe, J.: *A compact static double-array keeping character codes*.
  - _Information Processing and Management_ *43*(1) (January 2007) 237-247
- Yata, S., Morita, K., Fuketa, M., Aoe, J.: *Fast String Matching with Space-efficient Word Graphs*.
  - _Innovations in Information Technology (Innovations '08)_ Al Ain, United Arab Emirates (December 2008) 79-83

### Related Web Pages

- Jan Daciuk: *Jan Daciuk, Dept of Knowledge Engineering, Gdansk University of Technology*
  - http://www.eti.pg.gda.pl/katedry/kiw/pracownicy/Jan.Daciuk/personal/
- Sam Allen: *C# Directed Acyclic Word Graph*
  - http://dotnetperls.com/Content/Directed-Acyclic-Word-Graph.aspx
- Wutka Consulting, Inc.: *Directed Acyclic Word Graphs*
  - http://www.wutka.com/dawg.html
- Taku Kudo: *Darts: Double-ARray Trie System* (Japanese)
  - http://chasen.org/~taku/software/darts/
- Wikipedia: *Directed acyclic word graph - Wikipedia, the free encyclopedia*
  - http://en.wikipedia.org/wiki/Directed_acyclic_word_graph
- JohnPaul Adamovsky: *Directed Acyclic Word Graph or DAWG*
  - http://www.pathcom.com/~vadco/dawg.html
