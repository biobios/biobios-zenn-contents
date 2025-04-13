---
title: "【VSCode】競プロ用のC++開発環境をgithubで公開しました【Bloggerからの移行記事】"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cpp", "vscode", "競技プログラミング", "開発環境"]
published: true
published_at: 2024-07-06 18:04
---
いままで、VisualStudioを使って競プロをやっていたのですが、以下の点からVSCodeに乗り換えることにしました。

* AtCoderで使えるC++コンパイラ(gcc,Clang)とMSVCの違いのせいでコンパイルが通ったり通らなかったりする
* 競プロにVisualStudioはオーバースペック

乗り換えるために構築した環境を、githubに公開しているのでよかったら使ってください。  
@[card](https://github.com/biobios/CP-env)

## 利用方法

1.  あらかじめvscodeをインストールし、コンパイラとしてgccかclangをインストールする
2.  お好きな場所で`https://github.com/biobios/CP-env.git`をクローンorフォークしてクローン
3.  CP-env以下お好きな場所にc++ファイルを置いて作業する
4.  ctrl+shift+Bで現在開いているファイルをコンパイル
5.  F5でデバッグの開始

## 使用しているvscodeの機能

* [Tasks](https://code.visualstudio.com/docs/editor/tasks)
* [Snippets](https://code.visualstudio.com/docs/editor/userdefinedsnippets)
* [Launch configurations](https://code.visualstudio.com/docs/editor/debugging#_launch-configurations)

## 補足

自分はソースコードと開発環境を分けて管理したかったので、.gitignoreにsrcディレクトリやcontestsディレクトリが記述されています。

環境とソースコードを合わせて管理する場合は.gitignoreを編集してください。

## 最後に

改善案やバグ報告はIssueに上げてください。PRも大歓迎です。