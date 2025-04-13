---
title: "【C++】const修飾されたクラスのメンバポインタ【Bloggerからの移行記事】"
emoji: "😺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cpp"]
published: false
published_at: 2024-11-09 04:46
---
const修飾されたクラスのメンバポインタ型は普通コンパイルエラーで作ることができません。

```cpp
class test {};

using const_class_value_pointer = int test const::*;
using const_class_function_pointer = int (test const::*)(int);
```

型名はクラスを指していませんというエラーがでます。
しかし、以下のようにすればコンパイルを通すことができます。（テンプレートを用いても通すことができる）

```cpp
using c_test = const test;

using const_class_value_pointer = int c_test::*;
using const_class_function_pointer = int (c_test::*)(int);
```

まあ、参照の圧縮と同じような感じで`const`が勝手に外されるだけです。

```cpp

static_assert(is_same_v<int test::*, int c_test::*>);
static_assert(is_same_v<int (test::*)(int), int (c_test::*)(int)>);

```

同様に`volatile`も外されます。