---
layout: post
title:  "Light Weight Global Observable"
date:   2017-02-02 18:00:00 +0900
categories: blog
tags: swift
---
画面間でなんらかのデータの変化を共有したいとき、iOSでは`NSNotificationCenter`を使ってGlobalな値の更新をキャッチ＆表示の更新を行ったりすると思う。

OSが提供していて、それに乗っかるのは良いと思うんだけどキレイに書けないのが悩みの種だと思う。あと`NSObject`に限定されていてSwiftで書いているのに`@objc`とか書かないといけなくて「え〜？」ってなる人も多いと思う。

そんなこんなでこんな感じのコードを書けるものを作った。

```swift
let disposeBag = Notification.DisposeBag()

Notification.shared.post(.updated)
Notification.shared.subscribe(type: .updated) { [weak self] in /* 何かの処理 */ }
        .addDisposable(to: disposeBag)
```

Rxを見たことがある人には分かりやすいと思う。`post`でイベントを投げて、`subscribe`している箇所でそれを受け取って何かの処理をする。

`disposeBag`は不要なインスタンスの破棄のために必要。`subsribe`の戻り値が`Notification.Disposable`になっているので、この戻り値が使われていない時は警告が出るようになっている。

何かにべったり依存しないようにコードを書いていきたいのだけど、業務でRxを触っていたりすると、こういう仕組みがちょっとだけ欲しいときもある。

特に最初に説明したような画面館の更新とかまさにソレ。A画面からB画面に遷移して、B画面で情報が更新されたらA画面も更新する、みたいな状況。

コードは以下の通り。

```swift
import Foundation

final class Notification {

    enum Event {
        case updated
    }

    static let shared = Notification()
    private init() {}
    fileprivate var observers: [Observer] = []

    func post(_ type: Event) {
        for observer in observers {
            if observer.notificationType == type {
                observer.executeAction()
            }
        }
    }

    func subscribe(type: Event, action: @escaping () -> ()) -> Disposable {
        let observer = Observer(type: type, action: action)
        observers += [observer]
        return Disposable(observer)
    }

    class Observer {
        fileprivate let id = UUID().uuidString
        let notificationType: Notification.Event
        let executeAction: (() -> ())

        init(type: Notification.Event, action: @escaping () -> ()) {
            notificationType = type
            executeAction = action
        }
    }

    class DisposeBag {
        fileprivate var disposables: [Disposable] = []
    }

    class Disposable {
        private let id: String

        init(_ observer: Observer) {
            id = observer.id
        }

        func addDisposable(to bag: Notification.DisposeBag) {
            bag.disposables += [self]
        }

        deinit {
            Notification.shared.observers.enumerated()
                .filter { $1.id == id }
                .map { $0.0 }
                .forEach { index in
                    Notification.shared.observers.remove(at: index)
            }
        }
    }
}
```
Observerパターンなんだけどインターフェース = protocolを用意していない。

理由としては、単純に何らかのイベントが起こったことだけを知るために作って、どんなイベントが流れてくるかまでは知る必要がなかったから。`Observer`の`executeAction`の型を`(T)->()`にすればできるので、それは必要になった時にやればいいかなという感じ。

Light WeightなGlobal Observable。

ただしそういうのが増えていって自分で独自に実装するくらいならRxSwiftやReactiveCocoaの導入を考えても良いかもしれない。