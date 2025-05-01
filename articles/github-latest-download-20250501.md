---
title: "api.github.comを経由せずにGitHubの最新リリースをダウンロードする方法"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["github"]
published: true
---

最新のリリースをダウンロードするには、以下のようにすればいいです。

```bash
curl -LO https://github.com/<user>/<repo>/releases/latest/download/<file>
```

`<user>`はGitHubのユーザー名、`<repo>`はリポジトリ名、`<file>`はダウンロードしたいファイル名に置き換えてください。

なぜかわからないですが、軽く調べて見ても`api.github.com`を経由する方法しか出てこなかったんですよね。もしかしたらこの方法では問題があるのかもしれないですが、neovimのインストールスクリプトで使われていたのでたぶん大丈夫だと思います。