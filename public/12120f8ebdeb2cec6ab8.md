---
title: Ktor で Hello World ！
tags:
  - Kotlin
  - IntelliJ
  - Ktor
private: false
updated_at: '2019-09-28T11:09:27+09:00'
id: 12120f8ebdeb2cec6ab8
organization_url_name: yumemi
slide: false
ignorePublish: false
---
# はじめに
　今回は、人気急上昇中の言語 Kotlin の Web フレームワーク「Ktor」で "Hello World" していきます。「ケイター」って読むみたいです。(途中までコターって読むのかと思ってました。。笑)

## Ktor の特徴
　**Easy to use, fun and asynchronous.**

- 簡単
- 楽しい(ここ重要)
- 非同期

## 環境

|種類|名前|バージョン|
|---|---|---|
|OS|macOS Mojave|10.14.6|
|開発環境|[IntelliJ IDEA](jetbrains.com/idea/download/)|Ultimate 2019.2.3|
|開発言語|[Kotlin](https://kotlinlang.org/)|1.3.50|
|依存ライブラリ|[Ktor](https://ktor.io/)|1.2.4|

# IntelliJ IDEA の設定
　Jetbrain の IDE を利用したことがない方のために設定についても記述しておきます。

## カスタマイズ
　特にこだわりなければ「Skip Remaining and Set Default」をクリック
(UI Themes は Darcula が目に優しくおすすめです笑)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/44727d33-e6ab-289b-4c0a-be455fee51f8.png)

## ライセンス
　ライセンス購入していない方は30日間の無料期間があるので「Evaluate for free」を選択して「Evaluate」をクリック
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/c0387f37-e23c-73dd-cbca-e0bd37dd77e1.png)

## プラグイン
　「Configure」から「Plugins」をクリック
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/bf500b3d-d082-36a7-e7c7-285bad769b8d.png)

　「Marketplace」タブで「Ktor」と検索して、「install」して「Restart IDE」で再起動
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/a351b940-5ff0-1e2a-6a1a-29f0966f3308.png)

# Ktor プロジェクト作成

　「Create New Project」をクリック
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/ad2c1282-7027-7d40-3e89-39bafb9b7557.png)

　「Ktor」タブを選択して「Next」をクリック
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/cbb2b9fb-713c-371b-f9d8-7c9680d6a3c5.png)

　GroupId, ArtifactId, Version を入力して「Next」をクリック
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/7256bf4e-0152-1422-ba65-5dc10e7c1e79.png)

　Project name, Project location を入力して「Finish」をクリック
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/32632cb3-8fe8-2c0a-891a-b1c358ef0f35.png)

　「Automatically import this project on changes in build script file」にチェックを入れて「OK」をクリック
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/8eda3a1a-cc36-e13b-078b-c1ee68e6b867.png)

　プロジェクト作成完了！
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/e7a198f5-13fc-8dbf-3215-8eeb4c979090.png)

# Hello World
　Hello World を表示していきます！

## Plain Text
　生のテキストをブラウザで表示します。

```src/Application.kt
// 自分のパッケージ名
package com.example.helloworld

import io.ktor.application.*
import io.ktor.response.*
import io.ktor.request.*
import io.ktor.routing.get
import io.ktor.routing.routing

fun main(args: Array<String>): Unit = io.ktor.server.netty.EngineMain.main(args)

@Suppress("unused") // Referenced in application.conf
@kotlin.jvm.JvmOverloads
fun Application.module(testing: Boolean = false) {

    routing {
        get("/") {
            call.respondText { "Hello World" }
        }
    }
}
```

　mainメソッドの左側にある実行ボタンをクリックして「Run'~'」をクリック
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/5dd64c13-9e46-c32f-497a-cfde854c4ca9.png)

　すると下に http://0.0.0.0:8080 と出てくるのでこれをクリックしてブラウザを起動
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/14240d65-0e4e-ecc4-84eb-e3420782becc.png)

　Hello World !!
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/ae9da8df-e94c-e6fb-615f-f944f141e98d.png)

## HTML & CSS
　通常の開発では、生のテキストではなく HTML と CSS を組み合わせて表示しますよね。

```resources/hello_world.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Hello World</title>
    <link rel="stylesheet" type="text/css" href="/hello_world.css">
</head>
<body>

<h1>
    Hello World
</h1>

</body>
</html>
```
```resources/hello_world.css
h1 {
    color:red;
}
```
```src/Application.kt
// 自分のパッケージ名
package com.example.helloworld

import io.ktor.application.*
import io.ktor.http.content.default
import io.ktor.http.content.file
import io.ktor.http.content.static
import io.ktor.routing.routing

fun main(args: Array<String>): Unit = io.ktor.server.netty.EngineMain.main(args)

@Suppress("unused") // Referenced in application.conf
@kotlin.jvm.JvmOverloads
fun Application.module(testing: Boolean = false) {

    routing {
        static("/") {
            file("hello_world.css", "./resources/hello_world.css")
            default("resources/hello_world.html")
        }
    }
}
```

　同様に、mainメソッドの左側にある実行ボタンをクリックして「Run'~'」をクリックして、http://0.0.0.0:8080 をクリックしてブラウザ起動
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/6c18e610-b633-a8b0-01ef-ea6dc047f312.png)

## その他
　[HTML DSL](https://ktor.io/servers/features/templates/html-dsl.html)という動的に HTML を生成する方法もあるので、ぜひお試しあれ〜

# 最後に
　いかがでしたか？少しでも Kotlin, Ktor いいなと思ってくれていたら嬉しいです(^ω^)
ぜひ Kotlin, Ktor で開発やってみてください！

　最後までご視聴ありがとうございました。
