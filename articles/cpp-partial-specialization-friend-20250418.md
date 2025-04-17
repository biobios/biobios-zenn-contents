---
title: "部分特殊化した関数をフレンド関数に指定したかった"
emoji: "📘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cpp"]
published: true
published_at: 2024-04-18 03:00
---

## やりたかったこと

以下のようにして、部分特殊化した関数をフレンド関数に指定したかった。  
(行列クラスと行列積を計算する関数で、自身を引数として受け取る特殊化のみをフレンドにしたかった)

```cpp
template <typename T, typename U>
U foo(T, U) {
    // 処理
}

class A {
    template <typename T>
    friend A foo<T, A>(T, A);
};
```

しかし、[部分特殊化されたクラス・関数はフレンド指定できない](https://stackoverflow.com/questions/44213761/partial-template-specialization-for-friend-classes)ようだった。

## 代替案

結局、以下のようにして、任意の特殊化に対してフレンド指定をすることにした。

```cpp
template <typename T, typename U>
U foo(T, U) {
    // 処理
}

class A {
    template <typename T, typename U>
    friend U foo(T, U);
};
```

不要な範囲までアクセスを許可してしまうが、よく考えるとそれが問題になる場面はなさそうだ。