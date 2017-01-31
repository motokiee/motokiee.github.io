---
layout: post
title:  "Herokuでpostgresを使う heroku編"
date:   2017-01-31 19:00:00 +0900
categories: blog
tags: heroku golang
---
herokuにpostgresを用意したので、それを自分のアプリから接続したい。

今回はgormを使ってGoのWebアプリからpostgresに接続してみる。[gorm](http://jinzhu.me/gorm/database.html#connecting-to-a-database)のドキュメントを見てみる。

```golang
import (
    "github.com/jinzhu/gorm"
    _ "github.com/jinzhu/gorm/dialects/postgres"
)

func main() {
  db, err := gorm.Open("postgres", "host=myhost user=gorm dbname=gorm sslmode=disable password=mypassword")
  defer db.Close()
}
```

`host`, `user`, `dbname`などを引数に渡す。これらの情報はURLに含まれているので、まずはそれを確認する。

```shell
$ heroku config:get DATABASE_URL --app demoapp
```

この時に表示される情報は、`postgres://ユーザ名:パスワード@サーバ名:ポート/データベース`となっているので、照らし合わせてみる。

PG Commanderなどのデスクトップアプリを使ってこのDBを参照することもできて、GUIを使って直接DBを参照したり操作したい場合は便利。

環境変数があるのでうまいこと使いたいと思って調べたら[これ](http://stackoverflow.com/questions/36816553/how-to-properly-define-a-gorm-db-interface)が参考になった。

```golang
func connect() {
    databaseUrl := os.Getenv("DATABASE_URL")
    db, err := gorm.Open("postgres", databaseUrl)
    if err != nil {
        log.Fatal(err)
    }
}
```
こんな感じで行けそう。


herokuにdeployする時にherokuの設定ができていなかったので以下のようにgitに設定する。

```shell
$ heroku git:remote -a demoapp
$ git push heroku master
```

これでpushできた。が、buildに失敗した。エラーは以下の通りGoのバージョンがherokuでサポートされていないものだったから。

```
-----> Go app detected
-----> Checking Godeps/Godeps.json file.
 !!    
 !!    Deprecated or unsupported version of go (go1.5.4)
 !!    See https://devcenter.heroku.com/articles/go-support#go-versions for supported version information.
 !!    
-----> Using go1.5.4
 !!    Installing package '.' (default)
 !!    
-----> Running: go install -v -tags heroku . 
web.go:8:2: cannot find package "github.com/jinzhu/gorm" in any of:
	/app/tmp/cache/go1.5.4/go/src/github.com/jinzhu/gorm (from $GOROOT)
	/tmp/tmp.I8ddrnE8gP/.go/src/github.com/jinzhu/gorm (from $GOPATH)
web.go:9:2: cannot find package "github.com/jinzhu/gorm/dialects/postgres" in any of:
	/app/tmp/cache/go1.5.4/go/src/github.com/jinzhu/gorm/dialects/postgres (from $GOROOT)
	/tmp/tmp.I8ddrnE8gP/.go/src/github.com/jinzhu/gorm/dialects/postgres (from $GOPATH)
 !     Push rejected, failed to compile Go app.
 !     Push failed
```

きょうはここまで。次こそGoのバージョンを上げてデプロイしたい。