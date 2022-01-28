---
title: "た、たたたTypeScriptさん！？と思った話"
emoji: "💬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["typescript"]
published: false
---

# TypeScript を触っていてその仕様の多さ、わからなさに触れた

他人が書いた型定義を見て「こう書けるのか」「これは便利」という発見をすることがあっのと、[type-challenges](https://github.com/type-challenges/type-challenges)を解いていて「なにこれ！？」と思うことが多かったので、今回は迷子になりがちな TypeScript の言語仕様についていくつかピックアップしたいと思います。

## Tuple 型の T[number]について

[type-challenges](https://github.com/type-challenges/type-challenges)の`easy`で早速出てきた Tuple 型について、一見不思議な書き方に見える type を発見しました。

## never の使いどころ

`void`と`never`は戻り値がありません。この二つの違いは、関数が終了するかが異なります。
void は関数が最後まで実行されるという意味で、never は関数の処理が中断または永遠に続くことを意味しています。
文字で書かれると、void と never が違うことはわかりましたが、`never`の使い所ってどこなの？というと、素直に書けば以下のような関数の返り値に使えます。

```typescript
// throw文により最後まで到達しない
const throeError = (message: string): never => throw new Error(message);

// 無限ループ
// while(true)は no-constant-condition に引っかかるのでこの書き方にした
export const infinity = (): never => for (;;) console.log('infinity')
```

他の使い道としては、既存の型の中で Optional な型を never にして使えなくするという使い方をします。

https://typescriptbook.jp/reference/statements/never#void%E5%9E%8B%E3%81%A8never%E5%9E%8B%E3%81%AE%E9%81%95%E3%81%84
