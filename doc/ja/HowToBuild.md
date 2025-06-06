<!-- #summary 辞書構築の方法． -->

# 構築方法

## 基本辞書の構築

以下の例は，整列済みの語彙（キー集合）を構築する関数である．
まず，引数として与えられた入力ストリームからキーを一つずつ読み出し， DAWG に追加する．このとき，レコードにはデフォルトの値 0 を使用している．
次に，DAWG の構築を終了させ，辞書へと変換する．
そして，引数として与えられた出力ストリームに辞書を書き出す．

```cpp
#include <iostream>
#include <string>

#include <dawgdic/dawg-builder.h>
#include <dawgdic/dictionary-builder.h>

// 整列済みの語彙を入力ストリーム "lexicon_input" から読み出し，
// 辞書を構築して出力ストリーム "dic_output" へと書き出す．
bool BuildDictionary(std::istream *lexicon_input, std::ostream *dic_output) {

  // 語彙は辞書式昇順に整列されている必要がある．
  dawgdic::DawgBuilder dawg_builder;
  std::string key;
  while (std::getline(*lexicon_input, key)) {

    // 関数 Insert() に引数を一つだけ渡した場合，NULL 文字を終端とする
    // 文字列をキーとして，デフォルトのレコード 0 を関連付ける．
    if (!dawg_builder.Insert(key.c_str()))
      return false;

    // 第 2 引数にレコードを指定できる．
    // if (!dawg_builder.Insert(key.c_str(), record))
    //   return false;

    // キーの長さとレコードをそれぞれ第 2 引数，第 3 引数として指定できる．
    // if (!dawg_builder.Insert(key.c_str(), length, record))
    //   return false;

    // 注意：
    //   長さ 0 のキーは追加できない．
    //   NULL 文字を含むキーは追加できない．
    //   レコードは 0 以上の整数でなければならない．
  }

  // DAWG の構築を終了する．
  // このとき，構築用のオブジェクトは自動的に初期化される．
  dawgdic::Dawg dawg;
  dawg_builder.Finish(&dawg);

  // DAWG から辞書を構築する．
  dawgdic::Dictionary dic;
  if (!dawgdic::DictionaryBuilder::Build(dawg, &dic))
    return false;

  // 辞書を出力ストリームに書き出す．
  return dic.Write(dic_output);
}
```
