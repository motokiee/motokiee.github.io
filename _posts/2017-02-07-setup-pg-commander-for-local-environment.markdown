---
layout: post
title:  "PG CommanderでDBをGUIで管理する"
date:   2017-02-07 19:00:00 +0900
categories: blog
tags: postgres
---
DBをGUIで更新できたりすると便利なので[PG Commander](https://eggerapps.at/pgcommander/)を使ってみる。

ちなみにPG CommanderはApp Storeからでも利用することができます。無料だと5つまで接続先を作ることができる（はず）。

## Heroku Postgresに接続する 

まずはherokuのDBと接続してみる。以下のコマンドでDBの情報が取得できる。

```shell
heroku config:get DATABASE_URL
```

ランダムな文字列が返ってくるけど、`postgres://ユーザ名:パスワード@サーバ名:ポート/データベース`のように情報が羅列されているだけ。

PG Commanderで`Host`と書かれている部分にはサーバ名を入力する。以上でGUIでHeroku Postgresのデータを操作できるようになる。


## Localにpostgresを立ててPG Commanderで接続する
Mac環境でHomebrewを使ってpostgresをインストール。

```shell
$ brew install postgresql
```

インストールできたら初期化。

```shell
$ initdb /usr/local/var/postgres -E utf8
```

以下のコマンドでPostgreSQLサーバを起動。

```shell
postgres -D /usr/local/var/postgres
```

起動できたら以下のコマンドでデータベースの確認ができる。

```shell
$ psql -l
```

次はローカルとherokuで同じように接続できるように環境変数の設定を行います。[herokuでは環境変数`DATABASE_URL`が設定](https://motokiee.github.io/blog/2017/01/31/heroku-postgres-osEnv.html)していました。

アプリケーション側で`DATABASE_URL`を使ってDBに接続を行っているので、ローカルでも同じ環境変数を設定します。

`~/.bash_profile`に環境変数設定。

```
export DATABASE_URL=/usr/local/var/postgres
```

`source`で反映＆確認。

```shell
$ source ~/.bash_profile
$ echo $DATABASE_URL
```

ここまでやったんだけど、アプリケーションからDBに接続する際に色々とエラーが出てローカル環境から接続することはできず...していました。

また次の機会にもでも。