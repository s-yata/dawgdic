<!-- #summary クラスのインタフェース． -->

# ライブラリのインタフェース

## 主要なインタフェース

ライブラリ *dawgdic* の提供する主なクラスを以下に示す．

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
// 確保された DawgUnit の数を返す．
SizeType size() const;
```

基本的に *DawgUnit* は DAWG の遷移と対応するため，以下の関数 *num_of_transitions()* とほぼ同じ値を返す．

```cpp
// DAWG を構成する遷移の数を返す．
SizeType num_of_transitions() const;
// DAWG を構成する状態の数を返す．
SizeType num_of_states() const;
```

```cpp
// DAWG の構築において併合された遷移の数を返す．
SizeType num_of_merged_transitions() const;
// DAWG の構築において併合された状態の数を返す．
SizeType num_of_merged_states() const;
```

----

## DawgBuilder

| Header | Namespace |
|--|--|
| dawgdic/dawg-builder.h | dawgdic |

```cpp
// Dawg における同名の関数と同様の値を返す．
SizeType size() const;
SizeType num_of_transitions() const;
SizeType num_of_states() const;
SizeType num_of_merged_transitions() const;
SizeType num_of_merged_states() const;
```

```cpp
// キーを DAWG に登録する．
bool Insert(const CharType *key, ValueType value = 0);
bool Insert(const CharType *key, SizeType length, ValueType value);
```

キーは辞書順に登録する必要があり，
不正な順序でキーを登録しようとした場合は false を返す．
また，キーの長さ *length* が指定されているとき，キーに NULL 文字が含まれていれば false を返す．
一方， *length* が指定されていない場合，NULL 文字が出現するまでをキーの長さとして使用する．
レコード *value* は 0 以上 INT_MAX 以下の整数のみを受け付ける．

```cpp
// DAWG の構築を終了し，オブジェクトを初期化する．
bool Finish(Dawg *dawg);
```

すべてのキーを登録した後で呼び出す関数であり，完成した DAWG を *dawg* に渡す．
このとき，管理しているメモリを *dawg* に引き継ぐため，オブジェクト *this* は初期化されることになる．

----

## Dictionary

| Header | Namespace |
|--|--|
| dawgdic/dictionary.h | dawgdic |

```cpp
// 辞書を構成する配列の開始アドレスを返す．
const DictionaryUnit *units() const;
```

辞書は単一の配列によって表現されるため，この配列を保存しておくことにより，簡単に辞書を復元することができる．
なお，メモリ上の配列から辞書を復元するには関数 *Map()* を利用する．

```cpp
// 辞書を構成する配列の要素数を返す．
SizeType size() const;
// 辞書を構成する配列に割り当てなければならない最低限のメモリ量を byte 単位で返す．
SizeType total_size() const;
// ストリームに書き出されるサイズを byte 単位で返す．
SizeType file_size() const;
```

```cpp
// 辞書を入力ストリームから読み込む．
bool Read(std::istream *input);
// 辞書を出力ストリームに書き出す．
bool Write(std::ostream *output) const;
```

```cpp
// キーが辞書に登録されているかどうかを返す．
bool Contains(const CharType *key) const;
bool Contains(const CharType *key, SizeType length) const;
```

キーの長さ *length* が指定されていない場合， NULL 文字が出現するまでをキーと判断する．

```cpp
// キーが辞書に登録されているかどうかを確認し，登録されている場合には
// 関連付けられているレコードを返す．
ValueType Find(const CharType *key) const;
ValueType Find(const CharType *key, SizeType length) const;
bool Find(const CharType *key, ValueType *value) const;
bool Find(const CharType *key, SizeType length, ValueType *value) const;
```

レコードを受け取るための引数 *value* が指定されていない場合，レコードがそのまま返り値となる．
なお，与えられたキーが辞書に登録されていない場合，レコードとして -1 を返す．

```cpp
// 初期状態のインデックスを返す．
BaseType root() const;
// 与えられた状態がキーの終端になっているかどうかを返す．
bool has_value(BaseType index) const;
// 与えられた状態と対応するキーに関連付けられたレコードを返す．
ValueType value(BaseType index) const;
// 状態の遷移をおこない，インデックスを更新する．
bool Follow(CharType label, BaseType *index) const;
bool Follow(const CharType *s, BaseType *index);
bool Follow(const CharType *s, BaseType *index, SizeType *count);
bool Follow(const CharType *s, SizeType length, BaseType *index);
bool Follow(const CharType *s, SizeType length, BaseType *index, SizeType *count);
```

これらの関数は，前方一致検索のために提供されている．
検索においては，関数 *root()* により初期状態を取得した後，関数 *Follow()* によって文字あるいは文字列に対応する遷移を辿る．
そして，到達した状態がキーの終端になっているかどうかを関数 *has_value()* によって確認できる．
関数 *has_value()* が true を返した場合，関数 *value()* により関連付けられているレコードを取得できる．
使用例は HowToLookup に示されている．

```cpp
// 辞書を構成する配列として任意の領域を割り当てる．
void Map(const void *address);
void Map(const void *address, SizeType size);
```

配列の要素数 *size* が指定されていない場合，与えられた領域の先頭 4 bytes を要素数として使用する．

----

## DictionaryBuilder

| *Header* | dawgdic/dictionary-builder.h |
|--|--|
| *Namespace* | dawgdic |

```cpp
// DAWG から辞書を構築する．
static bool Build(const Dawg &dawg, Dictionary *dic, BaseType *num_of_unused_units = NULL);
```

*dawgdic::DawgBuilder* により構築した *dawg* から辞書を構築し，*dic* を新しい辞書に置き換える．
また，第 3 引数 *num_of_unused_units* が NULL でない場合，構築された辞書における未使用要素の数を代入する．

----

## Guide

| Header | Namespace |
|--|--|
| dawgdic/guide.h | dawgdic |

```cpp
// キー補完用の辞書を入力ストリームから読み込む．
bool Read(std::istream *input);
// キー補完用の辞書を出力ストリームに書き出す．
bool Write(std::ostream *output) const;
```

----

## GuideBuilder

| Header | Namespace |
|--|--|
| dawgdic/guide-builder.h | dawgdic |

```cpp
// DAWG と基本辞書からキー補完用の辞書を構築する．
static bool Build(const Dawg &dawg, const Dictionary &dic, Guide *guide);
```

*dawgdic::DawgBuilder* により構築した *dawg* と， *dawgdic::DictionaryBuilder* により構築した *dic* からキー補完用の辞書を構築し，*guide* を新しい辞書に置き換える．

----

## Completer

| Header | Namespace |
|--|--|
| dawgdic/completer.h | dawgdic |

```cpp
// オブジェクトを初期化する．
Completer();
// オブジェクトを初期化するとともに，キー補完に用いる辞書の組を設定する．
Completer(const Dictionary &dic, const Guide &guide);
```

```cpp
// 基本辞書を設定する．
void set_dic(const Dictionary &dic);
// キー補完用の辞書を設定する．
void set_guide(const Guide &guide);
```

```cpp
// 基本辞書への参照を取得する．
const Dictionary &dic() const;
// キー補完用の辞書への参照を取得する．
const Guide &guide() const;
```

```cpp
// キー補完の準備をおこなう．
void Start(BaseType index, const char *prefix = "");
void Start(BaseType index, const char *prefix, SizeType length);
```

与えられた状態 *index* を開始位置としてキー補完の準備をおこなう．
指定された *prefix* は開始位置に影響することなく，補完結果として得られる文字列の先頭に付加される．
開始位置 *index* に到達するまでの文字列を *prefix* として渡しておけば，関数 *Next()* によりキーを完全に復元することができる．

```cpp
// キー補完を逐次実行し，次のキーを復元する．
bool Next();
// 復元されたキーを NULL 文字を終端とする文字列として返す．
const char *key() const;
// 復元されたキーの長さを返す．
SizeType length() const;
// 復元されたキーに関連付けられているレコードを返す．
ValueType value() const;
```

----

## RankedGuide

| Header | Namespace |
|--|--|
| dawgdic/ranked-guide.h | dawgdic |

*Guide* と同様の関数を提供する．

----

## RankedGuideBuilder

| Header | Namespace |
|--|--|
| dawgdic/ranked-guide-builder.h | dawgdic |

*GuideBuilder* と同様の関数を提供する．

----

## RankedCompleter

| Header | Namespace |
|--|--|
| dawgdic/ranked-completer.h | dawgdic |

*Completer* と同様の関数を提供する．
