---
layout: post
title:  "DependencyInjectable"
date:   2017-02-15 21:00:00 +0900
categories: blog
tags: swift
---
ライトに安全に依存性の解決をしたいのでこんなprotocolを作った。

```swift
protocol DependencyInjectable {
  associatedtype Dependency
  var dependency: Dependency? { get set }
}
```

こいつをゴニョゴニョすることで型の制約が効くようになる。

例えば、こんな感じ。

```swift
class ViewController: UIViewController, DependencyInjectable {
  struct Dependency {
    let userId: Int
    static var empty: Dependency {
      return Dependency(userId: 0)
    }
  }
  var dependency: Dependency? = nil

  func instantiate(withDependency dependency: Dependency) -> ViewController {
    let vc = ViewController.fromStoryboard("Main") // Storyboardからインスタンス化するこういうメソッドがあるとする(実際はないので注意)
    vc.dependency = dependency
    return vc
  }
}
```

これはライトに作ることが目的なんだけど、`DependencyInjectable`な型の生成方法をもっときつく縛る場合は以下のようにする(同僚のアイデア)。

```swift
protocol DependencyInjectable {
  associatedtype Dependency
  static func make(dependency: Dependency) -> Self
}
```

これで`init`なんかを`private`にしてしまえば`DependencyInjectable`な型は`make`を使わない限り生成できなくなる。

これをStoryboardからのインスタンス化に応用してみる。例えば、上の方で出てきた`fromStoryboard`を`DependencyInjectable`と組み合わされた時だけ呼び出せるようにしてみる。

```swift
protocol FromStroyboard {}
extension FromStroyboard where Self: UIViewController, Self: DependencyInjectable {

    static func fromStoryboard(_ storyboardName: String) -> Self {
        let storyboard = UIStoryboard(name: storyboardName, bundle: nil)
        return storyboard.instantiateInitialViewController() as! Self
    }
}
```

で、さっきのViewControllerはこうする。

```swift
class ViewController: UIViewController, DependencyInjectable, FromStoryboard {
  struct Dependency {
    let userId: Int
    static var empty: Dependency {
      return Dependency(userId: 0)
    }
  }

  var dependency: Dependency? = nil

  func make(dependency: Dependency) -> ViewController {
    let vc = ViewController.fromStoryboard("Main")
    vc.dependency = dependency
    return vc
  }
}
```

違いとしては`FromStoryboard`をプロトコルに追加しただけなんだけど、このインスタンス化は`DependencyInjectable`に適合していない限りは呼び出すことができない。

もっとステキにできそうだけど、きょうはこの辺で。

あ、ちなみにこういう初期化ができる。

```swift
let vc = ViewController.make(dependency: .init(userId: 1111))
```

`.init`って書くことができる。なるほどね〜。

ちなみにコンパイラの力を借りずにコードを書いたのでコンパイルできないかもしれない...できなかったらtwitterで連絡ください。