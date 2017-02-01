---
layout: post
title:  "Herokuでpostgresを使う deploy編"
date:   2017-02-01 18:00:00 +0900
categories: blog
tags: heroku golang
---
きのうはdeployまでたどり着けなかったので再チャレンジ。rejectされてしまった。

```shell
 ! [remote rejected] master -> master (pre-receive hook declined)
```

ログを見ると、パッケージが見つからないことが原因だった。

```shell
remote: web.go:8:2: cannot find package "github.com/jinzhu/gorm" in any of:
```

ライブラリの参照で原因だった。[これ](https://github.com/tools/godep#how-to-use-godep-with-a-new-project)を参照に更新。

```
$ godep save
```

こんな感じで更新された。

```
        "ImportPath": "demoapp",
        "GoVersion": "go1.5",
        "GodepVersion": "v78",
-       "Deps": []
+       "Deps": [
+               {
+                       "ImportPath": "github.com/jinzhu/gorm",
+                       "Comment": "v1.0-115-g89f6d74",
+                       "Rev": "89f6d74b5ebab61a964b7a69c865a16cf9f24821"
+               },
+               {
+                       "ImportPath": "github.com/jinzhu/gorm/dialects/postgres",
+                       "Comment": "v1.0-115-g89f6d74",
+                       "Rev": "89f6d74b5ebab61a964b7a69c865a16cf9f24821"
+               },
+               {
+                       "ImportPath": "github.com/jinzhu/inflection",
+                       "Rev": "1c35d901db3da928c72a72d8458480cc9ade058f"
+               },
+               {
+                       "ImportPath": "github.com/lib/pq",
+                       "Comment": "go1.0-cutoff-143-g67c3f2a",
+                       "Rev": "67c3f2a8884c9b1aac5503c8d42ae4f73a93511c"
+               },
+               {
+                       "ImportPath": "github.com/lib/pq/hstore",
+                       "Comment": "go1.0-cutoff-143-g67c3f2a",
+                       "Rev": "67c3f2a8884c9b1aac5503c8d42ae4f73a93511c"
+               },
+               {
+                       "ImportPath": "github.com/lib/pq/oid",
+                       "Comment": "go1.0-cutoff-143-g67c3f2a",
+                       "Rev": "67c3f2a8884c9b1aac5503c8d42ae4f73a93511c"
+               }
+       ]
 }
```

そもそもなんにも管理されていないっぽかった。何にせよ行けそうだったので再push。

```shell
$ git push heroku master
```

コレで行けた！