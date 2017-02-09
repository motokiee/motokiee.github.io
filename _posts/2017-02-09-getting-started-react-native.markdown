---
layout: post
title:  "React Native 入門 環境構築編"
date:   2017-02-09 21:00:00 +0900
categories: blog
tags: reactnative
---
きょう会社でReact Nativeが話題になっていた。

[この記事が原因](https://engineering.instagram.com/react-native-at-instagram-dd828a9a90c7#.b8z5hfatx)で、Instagramが部分的にReact Nativeを取り入れているとのことだった

個人的に「React Native」な〜と思っていたものの、こうやって部分的に取り入れられるのであれば検証も可能なのでとても良いと思った。全部React Nativeで作るとなると全面的に舵を切れないといけないし、ソレはリスクなのでやりたくないよね、検証したいよね、という。

ということで早速調べてみた。あとこの内容はあさっての勉強会で発表しようと思っている。

## React Nativeとは
そもそもReact Nativeってなんですか？という話について。

React NativeはFacebookが提供するライブラリで、[リポジトリ](https://github.com/facebook/react-native)を見ると以下のように説明されている。

> React Native enables you to build world-class application experiences on native platforms using a consistent developer experience based on JavaScript and React. The focus of React Native is on developer efficiency across all the platforms you care about - learn once, write anywhere. Facebook uses React Native in multiple production apps and will continue investing in React Native.

JavaScriptとReactを使ってネイティブプラットフォームの開発ができる。コレが強みらしい。

やばい。俺どっちもよく知らない。

でもやると決めたからにはやるんです。なにより部分的に導入ができるので捨てようと思えばいつでも捨てられる。


ちなみに現段階（2017/02/09）のiOS/Androidのサポート対象は以下のようになっている。

> Supported operating systems are >= Android 4.1 (API 16) and >= iOS 8.0.

AndroidもiOSも十分っぽい（Androidはよく知らんけど）。

## Getting Started
ちゃんと「いろは」を説明するページが用意されている。[こちら](https://facebook.github.io/react-native/docs/getting-started.html#content)。

プラットフォームごとに環境設定が分かれている。同時にできると思っていたんだけど、iOSとAndroidで設定手順が異なる。両方やりたい人は両方やればOKらしい。

今回設定するのはiOS（iOS Developerなので）。

### Node.jsとWatchmanをインストール
ドキュメントだとHomebrewを推奨していた。

```shell
$ brew install node
$ brew install watchman
```
`node`は分かるとして`watchman`ってなんだろう？

`watchman`はこれまたFacebookの提供するライブラリで、ファイルシステム内の変更を監視するものらしい。ドキュメントでは効率よくするためのこれの導入を推奨している。

お次は`npm`でReact NativeのCLIをインストール。`npm`は`node`インストールで使えるようになっている（はず。間違ってたらゴメン）。

```shell
$ npm install -g react-native-cli
```

XcodeはMac App Storeからダウンロードしようね。

これでプロジェクトが作れるはず。

## プロジェクトを作ってみる

CLIでプロジェクトを作る。僕の場合は`~/Develop`以下にプロジェクトをまとめている（Goは`$GOPATH`になっているのでうまいこと統合したいのが悩み）のでそこに作る。

ドキュメントどおりにプロジェクト名は`AwesomeProject`で。

```shell
$ cd ~/Develop
$ react-native init AwesomeProject
```

めっちゃ何かを読み込む。30秒くらいかかった。

作ったプロジェクトのフォルダに移動して`react-native run-ios`を叩く。

```shell
$ cd AwesomeProject
$ react-native run-ios
```

とりあえずこれでビルド可能になる。iosフォルダにXcodeのプロジェクトが入っているのでいつも通り立ち上げてCmd+Rでシミュレータで実行すれば立ち上がる。

Xcode以外にも[Nuclide](https://nuclide.io/)というツールも使えるらしい（ちゃんと調べていないから分からん）。

index.ios.jsというファイルを修正しろと書いてあったのでやってみた。書き換えてシミュレータでCmd+Rするとリロードされて表示が変わる。

なにこれ便利...。

ちょっとだけコードを読んでみる。

```js
export default class AwesomeProject extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Hello React Native!
        </Text>
        <Text style={styles.instructions}>
          To get started, edit index.ios.js
        </Text>
        <Text style={styles.instructions}>
          Press Cmd+R to reload,{'\n'}
          Cmd+D or shake for dev menu
        </Text>
      </View>
    );
  }
}
```

`styles`という部分は下の方で見た目の定義がされていて、それを読み込んでいる。レイアウトについてもそこに書かれている。

`return`の次の行に書かれている`View`が`Text`をネストしているコンテナになっているっぽい。簡単なレイアウトならコレで良いかもしれない。実はシンプルなレイアウトでもAutolayoutで実装すると面倒なことがあるし。

## 以上

既存のコードに入れる方法は[Integration With Existing Apps](https://facebook.github.io/react-native/docs/integration-with-existing-apps.html)に書いてあるので、次はこれに目を通してみる。

一番やりやすいのは設定画面とかなのかな。これに通信とかが絡んできた時にどうやって実装するのかが目下知りたいことかな。