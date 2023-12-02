---
title: ゆめみの Flutter の研修課題を公開しました
tags:
  - 研修
  - 新人教育
  - Flutter
  - 新人プログラマ応援
  - 新人エンジニア
private: false
updated_at: '2022-12-01T07:03:16+09:00'
id: c2d6bdc4ebb8bbe8f666
organization_url_name: yumemi
slide: false
ignorePublish: false
---
## はじめに

株式会社ゆめみの Flutter の研修課題を公開しました :tada: 

https://github.com/yumemi-inc/flutter-training-template

ゆめみは、2022年1月に Flutter ギルドを立ち上げたばかりで、これまでドキュメント整備やテンプレートプロジェクトの作成、採用コーティング試験の作成など、少しずつギルドの体制を整えてきました。

そして、2023年4月に入社予定の方々のために、育成体制も着々と整えていっており、そのひとつとして研修課題を作成しました。

すでに、11月中旬から2名の内定承諾者アルバイトの方に取り組んでいただいており、今のところ「楽しい」「学びが多い」などの反応をいただけており嬉しい限りです :relaxed:

まだ作成したばかりで至らぬ点もあるかと思います。
何か気づいた点や改善点などありましたら、優しくご指摘いただけますと幸いです :pray:

## 内容

用意されている課題を順番にクリアして、天気予報アプリを開発していきます。

<img src=https://raw.githubusercontent.com/yumemi-inc/flutter-training-template/main/docs/sessions/images/json/demo.gif width=240 />

おおまかな内容は、ゆめみの iOS ギルドの研修課題と同じです。

https://qiita.com/watanave/items/1cefa5aae18f5141c426

## 対象者

難易度的には、ゆめみの Flutter の採用コーディング試験を通過した方を対象にしています。

しかし、ある程度環境構築を自動化しており、レイアウト構築から順番に進めていくため、初学者の方でも取り組みやすいものとなっています。

採用コーディング試験も公開していますので、ぜひご覧ください。

https://github.com/yumemi-inc/flutter-engineer-codecheck

## 事前準備

### インターネット環境

`flutter pub get` を実行するときや、そもそも課題を参照するためにも必要です。

### PC

Flutter や [fvm] がサポートしている OS であれば大丈夫です。

できれば iOS アプリの動作確認がしやすい macOS が良いです。

### エディタ

特に指定はありませんが、Flutter 公式で紹介されているもの方が、プラグインなどが整備されていて開発しやすいためおすすめです。

https://docs.flutter.dev/get-started/editor

個人的には、次のものを利用しています。

- [IntelliJ IDEA]
- [Visual Studio Code]
- [Xcode]

### メンター

それぞれの課題にヒントを用意してはいますが、それだけでは、どうしても解決できないことがあったり、深い学びを得られなかったりしてしまうかもしれません。

ゆめみでは、レビュー・フィードバック・フォローを行うメンターを必ず１名以上設定するようにしています。

### GitHub アカウント

基本的に、個人の GitHub アカウントのリポジトリに研修課題用のリポジトリを public で作成していただきます。

これによって、メンターも研修を受ける側も、よりエンジニアとしての活動が可視化され、いろいろな面でメリットがあるのではないかと考えています。

ただし、会社の方針や個人の考えによって、別の Git ホスティングサービスを使ったり、private リポジトリにしたりしても良いと思います。

## 進め方

詳しくは [README] をご覧ください。

ざっくり、次のように進めます。

1. 課題を進める
2. プルリクエストを提出して、メンターにレビューを依頼する
3. プルリクエストが承認されたらマージする

## 特徴

iOS の課題とだいたい同じなのですが、若干異なる部分がありますので紹介します。

### テンプレートリポジトリ

このリポジトリはテンプレートリポジトリに設定されています。

テンプレートリポジトリとして使用すると、自動的にワークフローが実行されるようになっており、次のことを行っています。

- 各 Session を Issue に登録
- fvm を利用して、最新安定板の Flutter SDK で Android・iOS 用のプロジェクトを作成
- lints を導入
- templates 配下のファイルを上書きコピー
- 不要なファイルを削除してコミット作成してプッシュ

そのため、環境構築の方法を統一でき、メンター・研修を受ける方のコストを削減できます。

### セットアップ

Session0 で研修課題を進めるにあたって必要なセットアップを行っていただきます。

GitHub のリポジトリ設定やプルリクエストチェックのワークフローの追加など、メンターの負担を少しでも軽くできるような工夫もここで行っています。

### Riverpod

この研修では、Riverpod を使うようにあえて制限を行っています。

状態管理に関するパッケージはいくつかありますが、Flutter 公式でも紹介されていたり、ドキュメントが充実していたり、テストしやすかったりとメリットが多く、ゆめみのプロジェクトでは推奨しています。

## おわりに

最近、Flutter でアプリ開発している会社が増えているように思いますが、Flutter のリードエンジニアの人数が少なく、育成どうしよう、、という声をよく聞きます。

その際は、ぜひ、この研修課題を利用していただけますと幸いです。

<!-- Links -->

[fvm]: https://fvm.app

[IntelliJ IDEA]: https://www.jetbrains.com/ja-jp/idea/old/

[Visual Studio Code]: https://azure.microsoft.com/ja-jp/products/visual-studio-code/

[Xcode]: https://apps.apple.com/jp/app/xcode/id497799835

[README]: https://github.com/yumemi-inc/flutter-training-template/blob/main/README.md
