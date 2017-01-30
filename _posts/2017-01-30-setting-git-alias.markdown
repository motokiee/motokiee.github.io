---
layout: post
title: "gitのaliasを設定する"
date: 2017-01-30 17:00:00 +0900
categories: blog
tags: git
---
gitのコマンドを打つのが面倒くさい。

```shell
$ git branch -a
```

```shell
$ git checkout .
```

ので、`alias`の設定。[こちらの記事](http://qiita.com/peccul/items/90dd469e2f72babbc106)を参考にさせてもらった。

ひとまず以下を設定。

```:~/.gitconfig
[alias]
	co = checkout	
	st = status	
	br = branch
	cm = commit 
```

クセで普通に入力してしまうんだけど、そのうち慣れるはず。一旦コレで続けてみて慣れたら増やしたい。

そう思う一方で、これってマシンごとに設定しておかないといけないのでこの設定に自分がロックアウトされてしまうのもちょっと嫌な感じ。

この辺共有できると良いんだけど良い方法はないかな。