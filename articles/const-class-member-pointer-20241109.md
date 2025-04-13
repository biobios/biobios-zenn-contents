---
title: "【C++】const修飾されたクラスのメンバポインタ【Bloggerからの移行記事】"
emoji: "😺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cpp"]
published: true
published_at: 2024-11-09 04:46
---
## 誤った方法

const修飾されたクラスのメンバポインタ型は普通コンパイルエラーで作ることができません。

```cpp
class test {};

using const_class_value_pointer = int test const::*;
using const_class_function_pointer = int (test const::*)(int);
```

型名はクラスを指していませんというエラーがでます。(`const test`はクラスではないらしい)  
しかし、以下のようにすればコンパイルを通すことができます。（テンプレートを用いても通すことができる）

```cpp
using c_test = const test;

using const_class_value_pointer = int c_test::*;
using const_class_function_pointer = int (c_test::*)(int);
```

結局のところ、参照の圧縮と同じような感じで`const`が勝手に外されるだけです。

```cpp

static_assert(is_same_v<int test::*, int c_test::*>);
static_assert(is_same_v<int (test::*)(int), int (c_test::*)(int)>);

```

同様に`volatile`も外されます。

## 結局どうすればいいのか

### メンバ変数

メンバ変数はオブジェクトのconst修飾の有無に関わらず、`int test::*`のようなメンバポインタで取得することができます。  
その際の戻り値のconst修飾の有無は、アクセスするオブジェクトのconst修飾の有無に準じます。

### メンバ関数

メンバ関数は、その宣言時のconst修飾の有無で、呼び出すオブジェクトのconst修飾を制限することができます。  
そのため、クラス名の部分ではなく関数型の部分でconst修飾をつけることでconst修飾されたクラスのメンバ関数ポインタを受けることができます。

```cpp

class foo {
public:
    int foo_function(int){
        return 1;
    }

    int foo_function(int) const{
        return 2;
    }
};

int (foo::*foo_function_pointer)(int) = &foo::foo_function;
int (foo::*const_foo_function_pointer)(int) const = &foo::foo_function;

foo a;
a.*foo_function_pointer(-1); // 1
a.*const_foo_function_pointer(-1); // 2

foo const b;
//b.*foo_function_pointer(-1); // コメントアウトを外すとコンパイルエラーになる
b.*const_foo_function_pointer(-1); // 2

```

ちなみに`int (foo::*)(int) const`型の`const`は関数型の方についていて、そのことは以下のコードでわかります。

```cpp

template<typename T>
class get_member_pointer_ret_type;

template<typename Ret, typename Class>
class get_member_pointer_ret_type<Ret Class::*>
{
public:
    using type = Ret;
}


static_assert(is_same_v<get_member_pointer_ret_type<int (foo::*)(int) const>::type, int (int) const>);

```
