---
layout: post
title:  "golangのfreshが便利だった"
date:   2017-01-24 20:35:00 +0900
categories: blog golang
---
GoでAPIサーバを作ってみようと思っていて環境構築をしていた。

コード書き終わったら、

```shell
$ go get 
$ PORT=5000 demoapp
```

みたいな感じでコマンドを入力するのが面倒でしょうがなかったんだけど、[fresh](https://github.com/pilu/fresh)を入れたら一発で楽になった。

コード変更したら

```shell
$ fresh
```

だけでOK。素晴らしい。