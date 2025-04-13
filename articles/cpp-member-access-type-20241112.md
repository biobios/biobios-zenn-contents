---
title: "【C++】メンバアクセス式の戻り値の型【Bloggerからの移行記事】"
emoji: "📘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cpp"]
published: true
published_at: 2024-11-12 04:17
---
そんな意外な話でもないのですが、右辺値のメンバにアクセスすると右辺値参照が、左辺値のメンバにアクセスすると左辺値参照が返ってきます。  
メンバポインタを使ったアクセスでも同様です。cv修飾は元の型のものを受け継ぎます。

```cpp
#include <utility>

using namespace std;

class Test {
public:
    int a = 0;
};

int main()
{
    int Test::* mem_ptr = &Test::a;
    Test test1 = Test();
    Test const test2 = Test();
    Test& test1_lref = test1;
    Test const& test2_lref = test2;
    Test&& test1_rref = Test();
    Test const&& test2_rref = Test();
    Test* test1_ptr = &test1;
    Test const* test2_ptr = &test2;
    
    // 通常のアクセス
    static_assert(is_same_v<decltype((Test().a)), int&&>); // int && : Testの右辺値
    static_assert(is_same_v<decltype((declval<Test&>().a)), int&>); // int & : Testの左辺値参照
    static_assert(is_same_v<decltype((declval<Test&&>().a)), int&&>); // int & : Testの右辺値参照(右辺値)
    static_assert(is_same_v<decltype((declval<const Test>().a)), const int &&>); // const int && : const Testの右辺値
    static_assert(is_same_v<decltype((declval<const Test&>().a)), const int &>); // const int & : const Testの左辺値参照
    static_assert(is_same_v<decltype((declval<const Test&&>().a)), const int &&>); // const int & : const Testの右辺値参照(右辺値)
    
    static_assert(is_same_v<decltype((test1.a)), int&>); // int & : Testの左辺値
    static_assert(is_same_v<decltype((test1_lref.a)), int&>); // int & : Testの左辺値参照
    static_assert(is_same_v<decltype((test1_rref.a)), int&>); // int & : Testの右辺値参照(左辺値)
    static_assert(is_same_v<decltype((test2.a)), const int&>); // const int & : Testの左辺値
    static_assert(is_same_v<decltype((test2_lref.a)), const int&>); // const int & : Testの左辺値参照
    static_assert(is_same_v<decltype((test2_rref.a)), const int&>); // const int & : Testの右辺値参照(左辺値)
    
    // メンバポインタによるアクセス
    static_assert(is_same_v<decltype(Test().*mem_ptr), int&&>); // int&& : Testの右辺値
    static_assert(is_same_v<decltype(declval<Test&>().*mem_ptr), int&>); // int& : Testの左辺値参照
    static_assert(is_same_v<decltype(declval<Test&&>().*mem_ptr), int&&>); // int&& : Testの右辺値参照(右辺値)
    static_assert(is_same_v<decltype(declval<const Test>().*mem_ptr), const int&&>); // const int&& : const Testの右辺値
    static_assert(is_same_v<decltype(declval<const Test&>().*mem_ptr), const int&>); // const int& : const Testの左辺値参照
    static_assert(is_same_v<decltype(declval<const Test&&>().*mem_ptr), const int&&>); // const int&& : const Testの右辺値参照(右辺値)
    
    static_assert(is_same_v<decltype(test1.*mem_ptr), int&>); // int& : Testの左辺値
    static_assert(is_same_v<decltype(test1_lref.*mem_ptr), int&>); // int& : Testの左辺値参照
    static_assert(is_same_v<decltype(test1_rref.*mem_ptr), int&>); // int& : Testの右辺値参照(左辺値)
    static_assert(is_same_v<decltype(test2.*mem_ptr), const int&>); // const int& : const Testの左辺値
    static_assert(is_same_v<decltype(test2_lref.*mem_ptr), const int&>); // const int& : const Testの左辺値参照
    static_assert(is_same_v<decltype(test2_rref.*mem_ptr), const int&>); // const int& : const Testの右辺値参照(左辺値)
    
    static_assert(is_same_v<decltype(test1_ptr->*mem_ptr), int&>); // int& : Test*
    static_assert(is_same_v<decltype(test2_ptr->*mem_ptr), const int&>); // const int& : Test const*

    return 0;
}
```

ちなみに関数の戻り値は、左辺値参照であれば左辺値、それ以外のときは右辺値のはずです。  
