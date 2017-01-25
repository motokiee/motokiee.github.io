---
layout: post
title:  "SwiftのdidSetはイニシャライザで呼ばれない"
date:   2017-01-25 21:00:00 +0900
categories: blog swift
---
Swiftで`didSet`を使う機会が多いんだけど、この前気がついたことがある。

それはイニシャライザ内で`didSet`が呼ばれないということ。何故か値が更新されずに困った。

[stackoverflowにワークアラウンドが記載されていて](http://stackoverflow.com/questions/25230780/is-it-possible-to-allow-didset-to-be-called-during-initialization-in-swift```)、回避方法としては`setVariable`みたいなセッターを用意すること。


```swift
class Person {
    var name: String {
        didSet {
            nameVariable.value = name
        }
    }
    var nameVariable = Variable<String>("")

    init(name: String) {
        self.name = name
        setVariable(name)
    }

    func setVariable(_ newValue: String) {
        nameVariable.value = newValue
    }
}
```


上記の方法は回りくどいだけなので、このような場合は以下のように初期化時に値をセットするだけで良いと思う。

```swift
class Person {
    var name: String {
        didSet {
            nameVariable.value = name
        }
    }

    var nameVariable = Variable<String>("")

    init(name: String) {
        self.name = name
        nameVariable.value = name
    }
}
```

ここまで書いて気づいたんだけど、イニシャライズする際に`didSet`が呼ばれないのは、`didSet`が呼ばれることによってどこで値がセットされているかわかりづらくなってしまうからなのかもしれない。

とは言え呼ばれて欲しい気持ちはある。