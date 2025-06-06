<!-- #summary 辞書検索の方法． -->

# 検索方法

## 完全一致検索

以下の例は，辞書からキーを検索する関数である．
NULL 文字を終端とする文字列が辞書に登録されているかどうかを確認し，その結果を標準出力ストリームに書き出す．

```cpp
#include <dawgdic/dictionary.h>

// キーの有無を確認する．
void Lookup(const dawgdic::Dictionary &dic, const char *key) {

  // キーが辞書に登録されているかどうかを確認する．
  if (dic.Contains(key))
    std::cout << key << ": " << ": found" << std::endl;
  else
    std::cout << key << ": " << ": not found" << std::endl;

  // キーが辞書に登録されているかどうかを確認し，
  // 登録されている場合はレコードを取り出す．
  int record;
  if (dic.Find(key, &record))
    std::cout << key << ": " << ": found (" << record << ')' << std::endl;
  else
    std::cout << key << ": " << ": not found" << std::endl;
}
```

## 前方一致検索

以下の例は，辞書に登録されているキーを検出する関数である．
NULL 文字を終端とする文字列を走査し，辞書に登録されているキーを検出した場合，それらを標準出力ストリームに書き出す．

```cpp
#include <string>

#include <dawgdic/dictionary.h>

// 文字列 "text" に出現するすべてのキーを見つける．
void Filter(const dawgdic::Dictionary &dic, const char *text) {

  for (const char *p = text; *p != '\0'; ++p) {

    // 始点を変更しつつ，前方一致検索をおこなう．
    dawgdic::BaseType index = dic.root();
    for (const char *q = p; *q != '\0'; ++q) {

      // 遷移を一つずつ辿る．
      if (!dic.Follow(*q, &index))
        break;

      // 検出したキーの情報を出力する．
      // 出力される内容は，キーの始点，長さ，レコード，文字列である．
      if (dic.has_value(index)) {
        std::cout << '(' << (p - text) << ", " << (q + 1 - p) << ", "
                  << dic.value(index) << "): ";
        std::cout.write(p, q + 1 - p) << std::endl;
      }
    }
  }
}
```
