---
layout: post
title:  "Herokuでpostgresを使う 準備編"
date:   2017-01-27 19:00:00 +0900
categories: blog
tags: heroku golang
---
まずはherokuのCLIをインストールする。[ここ](https://devcenter.heroku.com/articles/getting-started-with-go#set-up)からできる。

できたらログイン。

```shell
$ heroku login
Enter your Heroku credentials.
Email: hogehoge@example.com
Password (typing will be hidden): 
Logged in as hogehoge@example.com
```

データベースの追加方法は[ここ](https://devcenter.heroku.com/articles/getting-started-with-go#use-a-database)に書かれているけど、CLIじゃなくてもできるみたいで、[ここから](https://elements.heroku.com/addons/heroku-postgresql?app=blooming-sierra-75538)対象アプリにHeroku postgresqlを追加した。

追加したデータベースの情報は、

```shell
$ heroku config --app demoapp
```

で確認できる。ここに表示されるURLをアプリに記載すれば良いはず。

`demoapp`を自分のアプリ名に書き換えればOK。

[このページ](https://devcenter.heroku.com/articles/getting-started-with-go#use-a-database)だとコマンドになんのオプションも付けてないんだけど、
基本的には`--app`をつけてアプリ名も指定するらしい。

以下のコマンドでDBのプランが確認できる。
```
$ heroku pg --app demoapp
```

サンプルコードを見ると、`DATABASE_URL`環境変数で取り出していたので、アプリの方もこの環境変数を使いたい。

```shell
$ heroku config:get DATABASE_URL --app demoapp
```

環境変数は上記のコマンドで確認することができる。

次はこれを実装してherokuのDBと接続する。