---
title: "TypeScriptとインデックスシグネチャの話"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["typescript"]
published: true
---

## インデックスシグネチャとは

オブジェクトなどのデータ構造に添字でアクセスする仕組みのことです。

## JavaScript のインデックスシグネチャ

`JavaScript` の `Object` では参照を保持し、下記のように文字列で値にアクセスすることができます。

```javascript
const fruitsColor = {
  orange: "orange",
  melon: "green",
  peach: "pink",
};
console.log(fruits["melon"]); // green
```

また、`JavaScript` はインデックスシグネチャにオブジェクトを渡すと場合、暗黙的に `toString` を呼び出します。

```javascript
const obj = {
  toString() {
    console.log("fruits");
    return "red";
  },
};
let fruitsColor = { orange: "orange" };
fruitsColor[obj] = "purple"; // fruits
console.log(foo[obj]); // fruits, purple
```

value に `string` でアクセスが可能で、`Object` を渡すと暗黙的に `string` に変換されて実行されます。
それに対し配列は数値インデックスで値にアクセスが可能です。オブジェクトには数値でアクセスしても `number` 型のインデックスは振られていないので`undefined`が返されます。

```javascript
const colors = ["orange", "green", "pink"];
console.log(colors[0]); // orange

const fruitsColor = {
  orange: "orange",
  melon: "green",
  peach: "pink",
};
console.log(fruits[0]); // undefined
```

## TypeScript のインデックスシグネチャ

TypeScript のインデックスシグネチャは`string`,`number`(または `symbol`)型のいずれかでなければなりません。
その他の型を渡すとコンパイルエラーになります。
そして、インデックスシグネチャを明示的に型定義するときは以下のように書くことが可能です。

```typescript
type SomethingObject = {
  [key: string]: number;
};
let obj: SomethingObject;
obj = { a: 1, b: 2 }; // OK
obj.c = 4; // OK
obj["d"] = 5; // OK
```

ちなみにユーティリティータイプを使っても同じ型を作ることができます。

```typescript
type SomethingObject = Record<string, number>;
```

## Tuple 型や配列を インデックスシグネチャの[number]を使って Union 型にする

[type-challenges](https://github.com/type-challenges/type-challenges)の easy で早速出てくる `Tuple` 型について、一見不思議な書き方に見える type が出てきます。

```typescript
type Fruits = ["orange", "melon", "peach"];
type UnionFruits = Fruits[number]; // "orange" | "melon" | "peach"

const directions = ["east", "west", "south", "north"] as const;
type UnionDirections = typeof directions[number]; // "east" | "west" | "south" | "north"
```

上記のように`key`に`number`を設定すると、配列の値が全てユニオンされます。上記の`UnionFruits`と`UnionDirections`は、`number`型でアクセスできる値全てをユニオンします。
配列は`directions[0], directions[1]`という形でインデックスにアクセスできるため上記のような記載が可能になっています。
ちなみに`Fruits[string]`や`typeof directions[string]`ではコンパイルエラーになります。

---

インデックスシグネチャが調べてみると意外とややこしい仕組みでした。

https://typescript-jp.gitbook.io/deep-dive/type-system/index-signatures
https://typescriptbook.jp/reference/values-types-variables/object/index-signature
