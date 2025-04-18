---
title: "メンバ関数のCV修飾子と参照修飾子の挙動"
emoji: "💭"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cpp"]
published: true
published_at: 2025-04-19 00:00
---

以下を初期状態とする。

```cpp
struct Foo
{
    // &記号有りと無しは共存できない
    // void bar() {
    //     cout << "no specifier" << endl;
    // }
    // void bar() const {
    //     cout << "const specifier" << endl;
    // }
    void bar() const & {
        cout << "const& specifier" << endl;
    }
    void bar() const && {
        cout << "const&& specifier" << endl;
    }
    void bar() & {
        cout << "& specifier" << endl;
    }
    void bar() && {
        cout << "&& specifier" << endl;
    }
};
```

## 左辺値で呼び出すとき

初期状態のときは、`void bar() &`が呼ばれる。  
`void bar() &`をコメントアウトすると、`void bar() const &`が呼ばれる。  
`void bar() const &`をコメントアウトすると、コンパイルエラーになる。  
`&&`と`const&&`の関数をコメントアウトして、指定子なしと`const`のついた関数のコメントアウトを外すと、指定子なしが呼ばれる。  
指定子なしの関数をコメントアウトすると、`const`のついた関数が呼ばれる。  

## 右辺値で呼び出すとき

初期状態のときは、`void bar() &&`が呼ばれる。  
`void bar() &&`をコメントアウトすると、`void bar() const &&`が呼ばれる。  
`void bar() const &&`をコメントアウトすると、`void bar() const&`が呼ばれる。  
`void bar() const&`をコメントアウトすると、コンパイルエラーになる。  
`void bar() &`をコメントアウトして、指定子なしと`const`のついた関数のコメントアウトを外すと、指定子なしが呼ばれる。  
指定子なしの関数をコメントアウトすると、`const`のついた関数が呼ばれる。  

## const左辺値で呼び出すとき

初期状態のときは、`void bar() const&`が呼ばれる。  
`void bar() const&`をコメントアウトすると、コンパイルエラーになる。  
指定子なしと`const`のついた関数のみを残すと、`void bar() const`が呼ばれる。  
`void bar() const`をコメントアウトすると、コンパイルエラーになる。  

## const右辺値で呼び出すとき

初期状態のときは、`void bar() const&&`が呼ばれる。  
`void bar() const&&`をコメントアウトすると、`void bar() const&`が呼ばれる。  
`void bar() const&`をコメントアウトすると、コンパイルエラーになる。  
指定子なしと`const`のついた関数のみを残すと、`void bar() const`が呼ばれる。  
`void bar() const`をコメントアウトすると、コンパイルエラーになる。

## まとめ

優先順位
| オブジェクト | 1番目 | 2番目 | 3番目 | 4番目 | 5番目 |
| --- | --- | --- | --- | --- | --- |
| 左辺値 | `&` | `const&` | 指定子なし | `const` | |
| 右辺値 | `&&` | `const&&` | `const&` | 指定子なし | `const` |
| const左辺値 | `const&` | `const` | | | |
| const右辺値 | `const&&` | `const&` | `const` | | |