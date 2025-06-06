<!-- #summary 最初にお読みください． -->
<!-- #labels Phase-Design,Featured -->

# DAWG 辞書 C++ ヘッダライブラリ

## はじめに

本プロジェクトは，Directed Acyclic Word Graph (DAWG) による
辞書を実現するライブラリ *dawgdic* を提供します．

DAWG はトライ（Trie）の共通部分木を併合したグラフ構造であり，
トライを決定性有限オートマトン（DFA）とみなすとき，
最小決定性有限オートマトン（MDFA）に相当します．
そのため，トライと同等の検索性能を維持しつつ，
メモリ使用量においては，トライよりも優れています．
さらに，*dawgdic* では，DAWG の実装にダブル配列を用いているため，
ダブル配列によるトライのライブラリ *Darts* と比較しても，
遜色ない検索速度を実現することができるという特徴があります．

- Documentation
  - [README](README.md): 最初にお読みください．
    - [HowToBuild](HowToBuild.md): 辞書構築の方法に関するドキュメントです．
    - [HowToLookup](HowToLookup.md): 辞書検索の方法に関するドキュメントです．
    - [HowToComplete](HowToComplete.md): キー補完の方法に関するドキュメントです．
  - [Interface](Interface.md): クラスのインタフェースに関するドキュメントです．

----

## インストール

### 基本的なインストール方法

Unix 系の環境では，configure と make によるインストールが可能です．

```cpp
./configure
make
make install
```

上記の方法により，2 つのコマンド *dawgdic-build* と *dawgdic-find* に加えて，
C++ ヘッダライブラリとして *<dawgdic/`*`.h>* がインストールされます．
各コマンドとヘッダの概要は以下の通りです．

- コマンド
  - *dawgdic-build*
    - 語彙（キー集合）から辞書を構築します．
    - 入力ファイルの（LF を終端とする）各行がキーとして登録されます．
    - キーは辞書順に整列しておく必要があります．
  - *dawgdic-find*
    - 入力行の先頭に出現するキーを見つけます．
 - ヘッダ
   - *<dawgdic/`*`.h>*
     - 辞書を構築するためのクラスを提供します．
     - キーを検索するためのクラスを提供します．
     - キーを補完するためのクラスを提供します．

### インストールせずに使う方法

Windows 環境で configure スクリプトを利用できない場合など，
インストールをせずに *dawgdic* を使いたいときは，
src/dawgdic 以下のヘッダをプロジェクトのディレクトリに直接コピーしてください．

----

## 使い方

### 辞書の構築

辞書の構築手順は以下のようになります．

- DAWG の構築
  - キー集合から単純な DAWG を構築します．
    - キーは辞書順に一つずつ登録します．
    - *dawgdic::DawgBuilder* が DAWG 構築用のクラスです．
    - *dawgdic::Dawg* が DAWG のクラスです．
- DAWG の変換
  - 単純な DAWG をダブル配列による辞書へと変換します．
    - *dawgdic::DictionaryBuilder* が辞書構築用のクラスです.
    - *dawgdic::Dictionary* が辞書のクラスです．

以下の例では，2 つのキー "apple" と "orange" に対する辞書を構築しています．
キーの登録時に引数を省略するとデフォルトの値 0 がレコードとして
割り当てられますが，必要に応じてユーザが設定することも可能です．
その場合，*dawgdic::DawgBuilder* のメンバ関数 *Insert()* に対し，
第 2 引数もしくは第 3 引数として渡すことになります．

```cpp
#include <ofstream>

#include <dawgdic/dawg-builder.h>
#include <dawgdic/dictionary-builder.h>

// キーを DAWG に登録する．
dawgdic::DawgBuilder dawg_builder;
dawg_builder.Insert("apple");
dawg_builder.Insert("orange");

// DAWG を完成させる．
dawgdic::Dawg dawg;
dawg_builder.Finish(&dawg);

// DAWG を辞書に変換する．
dawgdic::Dictionary dic;
dawgdic::DictionaryBuilder::Build(dawg, &dic);

// 辞書をファイル "test.dic" に書き出す．
std::ofstream dic_file("test.dic", ios::binary);
dic.Write(&dic_file);
```

=== 辞書へのアクセス ===

以下の例では，辞書からキーを検索しています．
入力文字列の先頭に一致するキーを検索（前方一致検索）する場合は，
*dawgdic::Dictionary* のメンバ関数 *Follow()* を利用してください．

```cpp
#include <ifstream>
#include <iostream>

#include <dawgdic/dictionary.h>

// 辞書をファイル "test.dic" から読み込む．
std::ifstream dic_file("test.dic", ios::binary);
dawgdic::Dictionary dic;
dic.Read(&dic_file);

// キーの有無のみを確認する．
if (dic.Contains("apple"))
  std::cout << "apple: found" << std::endl;

// キーを検索してレコードを取得する．
if (dic.Find("banana") < 0)
  std::cout << "banana: not found" << std::endl;
```

----

## 参考文献

### 関連論文

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

### 関連 URL

- Jan Daciuk: *Jan Daciuk, Dept of Knowledge Engineering, Gdansk University of Technology*
  - http://www.eti.pg.gda.pl/katedry/kiw/pracownicy/Jan.Daciuk/personal/
- Sam Allen: *C# Directed Acyclic Word Graph*
  - http://dotnetperls.com/Content/Directed-Acyclic-Word-Graph.aspx
- Wutka Consulting, Inc.: *Directed Acyclic Word Graphs*
  - http://www.wutka.com/dawg.html
- Taku Kudo: *Darts: Double-ARray Trie System*
  - http://chasen.org/~taku/software/darts/
- Wikipedia: *Directed acyclic word graph - Wikipedia, the free encyclopedia*
  - http://en.wikipedia.org/wiki/Directed_acyclic_word_graph
- JohnPaul Adamovsky: *Directed Acyclic Word Graph or DAWG*
  - http://www.pathcom.com/~vadco/dawg.html
