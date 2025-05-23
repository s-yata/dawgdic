<!-- #summary キー補完の方法． -->

# キー補完方法

## キー補完用辞書の構築

以下の例は，DAWG と基本辞書からキー補完用の辞書を構築する関数である．
以下の関数で使用している *dawgdic::Guide* と *dawgdic::GuideBuilder* は，辞書式昇順にキーを補完するための辞書を提供する．
レコード降順にキーを補完する場合，代わりに *dawgdic::RankedGuide* と *dawgdic::RankedGuideBuilder* を利用する必要がある．

```cpp
#include <iostream>

#include <dawgdic/guide-builder.h>

// 構築済みの DAWG "dawg" と基本辞書 "dic" からキー補完用の辞書を構築し，
// 出力ストリーム "guide_output" に書き出す．
bool BuildGuide(const dawgdic::Dawg &dawg, const dawgdic::Dictionary &dic,
  std::ostream *guide_output) {

  // キー補完用の辞書 "guide" を構築する．
  dawgdic::Guide guide;
  if (!dawgdic::GuideBuilder::Build(dawg, dic, &guide))
    return false;

  // キー補完用の辞書を出力ストリームに書き出す．
  return guide.Write(guide_output);
}
```

## キー補完

以下の例は，特定の文字列で始まるキーを辞書から見つけ出す関数である．
基本辞書に加えてキー補完用の辞書を用いることにより，総当たりを避け，短時間でキー補完を実現している．
なお，キー補完用の辞書構築と同様に，レコード降順にキーを補完する場合，別のクラス *dawgdic::RankedGuide* と *dawgdic::RankedCompleter* を利用する必要がある．

```cpp
#include <iostream>

#include <dawgdic/completer.h>

// 基本辞書 "dic" とキー補完用の辞書 "guide" を利用し，
// NULL 文字を終端とする文字列 "prefix" で始まるキーを，
// 最大 "top_n" 個まで標準出力ストリームに書き出す．
void Complete(const dawgdic::Dictionary &dic, const dawgdic::Guide &guide,
  const char *prefix, int top_n) {

  // 与えられた文字列 "prefix" で始まるキーの存在を確認するとともに，
  // キー補完の開始位置となる状態を求める．
  dawgdic::BaseType index = dic.root();
  if (!dic.Follow(prefix, &index))
    return;

  // キー補完用のオブジェクトを初期化する．
  dawgdic::Completer completer(dic, guide);

  // キー補完の準備をおこなう．
  completer.Start(index, prefix);

  for (int i = 0; i < top_n; ++i) {

    // 補完により次のキーを取得する．
    // 既にすべてのキーを補完していれば終了する．
    if (!completer.Next())
      break;

    // 補完により得られたキーとレコードの組を出力する．
    std::cout << completer.key() << ": " << completer.value() << std::endl;
  }
}
```
