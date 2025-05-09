# C++ Header Library of DAWG Dictionary Algorithm

This project provides a library **dawgdic** for building and accessing dictionaries implemented with directed acyclic word graphs (DAWG).

A dawg is constructed by minimizing a trie as a deterministic finite automaton (DFA), and thus the dawg has an advantage in memory usage.
In addition, **dawgdic** uses a double-array as a base data structure, so its retrieval speed is as fast as that of **Darts**, a library for building and accessing double-array tries.

- Documentation
  - [README](doc/README.md): Please read this document first.
    - [HowToBuild](doc/HowToBuild.md): Examples to build a dictionary from a sorted lexicon.
    - [HowToLookup](doc/HowToLookup.md): Examples to lookup keys in a dictionary.
    - [HowToComplete](doc/HowToComplete.md): Examples to complete keys from a given prefix.
  - [Interface](doc/Interface.md): This document describes class interfaces.
- Related projects
  - A Python wrapper. (https://github.com/kmike/DAWG)

----

# DAWG 辞書 C++ ヘッダライブラリ

本プロジェクトは，Directed Acyclic Word Graph (DAWG) による辞書を実現するライブラリ **dawgdic** を提供します．

DAWG はトライ（Trie）の共通部分木を併合したグラフ構造であり，トライを決定性有限オートマトン（DFA）とみなすとき，最小決定性有限オートマトン（MDFA）に相当します．
そのため，トライと同等の検索性能を維持しつつ，メモリ使用量においては，トライよりも優れています．
さらに， **dawgdic** では， DAWG の実装にダブル配列を用いているため，ダブル配列によるトライのライブラリ **Darts** と比較しても，遜色ない検索速度を実現することができるという特徴があります．

- Documentation
  - [README](doc/ja/README.md): 最初にお読みください．
    - [HowToBuild](doc/ja/HowToBuild.md): 辞書構築の方法に関するドキュメントです．
    - [HowToLookup](doc/ja/HowToLookup.md): 辞書検索の方法に関するドキュメントです．
    - [HowToComplete](doc/ja/HowToComplete.md): キー補完の方法に関するドキュメントです．
  - [Interface](doc/ja/Interface.md): クラスのインタフェースに関するドキュメントです．
- Related projects
  - Python ラッパーです．(https://github.com/kmike/DAWG)

