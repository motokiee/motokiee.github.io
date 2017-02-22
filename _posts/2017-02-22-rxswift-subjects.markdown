---
layout: post
title:  "RxSwiftのSubjectについて"
date:   2017-02-22 21:00:00 +0900
categories: blog
tags: rxswift
---
きょうは英会話でめちゃくちゃ怖い先生にあたって凹んでいるので、 Tour of Go を読むのは一旦お休み。

※途中で`Element`と"値"という言葉が混在してるけど同じ意味。

## RxのSubjectについて
Subjectは`Observer`でもあり`Observable`でもある。つまり`Element`を流すこともできるし`subscribe`することもできる。

そしてSubjectは、

- `PublishSubject`
- `ReplaySubject`
- `BehaviorSubject`

の3つ存在する。

### PublishSubject

`PublishSubject`は`subscribe`後の`Element`だけを流す。`BehaviorSubject`との違いは過去の値が流れてこないこと。

`subscribe`したあとだけ`Element`が流れてくる。

### BehaviorSubject

`subscribe`されたタイミングで最新の値を流す。`BehaviorSubject`を初期化する際は初期値を設定するようになっていて、
何も`Element`が流れていない場合は初期値を流すようになっている。

つまり`subscribe`したタイミングで何らかの値（初期値 or 過去の最新の値）が流れるようになっている。


```swift
let behaviorSubject = BehaviorSubject<Int>(value: 1)
```

### ReplaySubject
`subscribe`後に値を流す。`bufferSize`を指定するようになっていて、`bufferSize`に指定した数だけ`Element`だけを流す。

```swift
let replaySubject = ReplaySubject<Int>.create(bufferSize: 1)
```

指定したサイズの数だけ`Element`をbufferしといて、`subscribe`されたらそれを流す感じ。

`BehaviorSubject`は`bufferSize`を`1`に設定したもの。


### Variable

`BehaviorSubject`をラップしている`Variable`というものもあって、プロパティのようにget/setで値が流れるようになる。

```swift
class Some {
    let variable = Variable<Int>(1)
}
```

明日は`share()`と`shareReplay(n)`の違いについて掘り下げてみるつもり。