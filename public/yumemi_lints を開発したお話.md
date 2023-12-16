---
title: yumemi_lints を開発したお話
tags:
  - Dart
  - OSS
  - lint
  - Flutter
private: true
updated_at: '2023-12-17T00:50:20+09:00'
id: e2006e56b2c008f09cc6
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

2023 年 11 月に pub.dev でゆめみ初のパッケージを公開しました。

https://pub.dev/packages/yumemi_lints

https://github.com/yumemi-inc/flutter-yumemi-lints

## [yumemi_lints] の特徴

ざっくり特徴をまとめると次のとおりです。

- Dart / Flutter のバージョンごとのリントルールセットを提供しています
- Dart 2.17 / Flutter 3.0.0 以降の全てのバージョンをサポートしています
- 提供しているリントルールセットは自動生成しています
- 推奨リントルールセット以外に全てのルールが入っているリントルールセットも提供しているため、簡単にカスタマイズできます

詳しくは [yumemi_lints] の README をご覧ください。

## 開発することになったきっかけ

個人的には、2022 年 1 月に Flutter ギルドを立ち上げた当初から、会社として pub.dev へ何かパッケージを公開したいと考えていました。

しかし、まだまだギルドの規模が小さく、Flutter に関する開発案件も少なかったため、ゆめみとしてパッケージを開発することは先送りにしていました。

ですので、まずはコーディング試験・研修課題などの採用・育成関連の整備を進め、Flutter ギルドの規模を拡大することを優先していました。

https://github.com/yumemi-inc/flutter-engineer-codecheck

https://github.com/yumemi-inc/flutter-training-template

中途採用や新卒メンバーの育成が順調に進んできた 2023 年 5 月ごろ、とあるメンバーとのやりとりがきっかけとなって OSS WG を立ち上げることになりました。

どのような OSS を開発するかいくつか案が出ましたが、はじめはできる限りシンプルなパッケージの方が良さそうということで、ゆめみの推奨リントルールをまとめたパッケージを開発することになりました。

しかし、次のようなステキなパッケージがある中、シンプルすぎるパッケージでは開発するメリットがあまりありません。

https://pub.dev/packages/pedantic_mono

https://pub.dev/packages/very_good_analysis

一方で、既存のパッケージをゆめみとして推奨するかというと少し難しい側面がありました。

## 既存パッケージをゆめみとして推奨することが難しい理由

あくまで、ゆめみとして推奨することが難しいというだけですので、パッケージに問題があるというわけではありません。ご注意ください。

### パッケージの更新を気軽に行うことができない

ゆめみでは内製化支援を行っている性質上、異なる Dart / Flutter バージョンで開発を進めることが多くあります。

ですので、パッケージを導入する際はパッケージの最新バージョンを選べばいいというわけではなく、導入するプロジェクトの Dart / Flutter のバージョンに合わせてパッケージのバージョンを選ぶ必要があります。

パッケージの適切なバージョンを選ぶためには、次のようにします。

1. プロジェクトの Dart / Flutter のバージョンを確認する
2. Flutter に紐づく Dart のバージョンを確認する
3. パッケージの pubspec.yaml を見て、Dart / Flutter の最小バージョンを確認する
4. パッケージで使用されているリントルールが追加された Dart バージョンを確認する

特に 4 の確認が大変です。
例えば `annotate_redeclares` は Dart 3.2 で追加されたリントルールですので、Dart 3.1 で使用するとエラーになってしまいます。

https://dart.dev/tools/linter-rules/annotate_redeclares

次のページを確認すれば、一覧で確認することができますが、バージョンを更新する度にこのような確認をしなければならないのは少し大変です。

https://github.com/dart-lang/sdk/blob/80ad96837c207bb1d74c8c54e7387b9e6b7093df/pkg/linter/tool/since/sdk.yaml

そのため [yumemi_lints] では Dart / Flutter のバージョンごとにリントルールセットを提供しています。

### Flutter 固有のリントルールが含まれている

ゆめみでは Flutter プロジェクトだけでなく、Dart プロジェクトも取り扱っています。

Dart プロジェクトに Flutter 固有のリントルールが含まれていると、余計なリントチェックが実行されたり、余計な警告が出てしまったりしてしまいます。

そのため [yumemi_lints] では Dart / Flutter でリントルールセットを分けて提供するようにしています。

## リントルールセットの自動生成の仕組み

上記のようなリントルールセットを提供するために、手動でリントルールセットを作成するのは大変です。

ですので、[yumemi_lints] の GitHub プロジェクトの `tools` ディレクトリの中に、リントルールセットを自動生成するための Dart CLI プロジェクトを作成しました。

そこで行っている大まかな流れは次のとおりです。

1. [リントルールの情報元] から非推奨・削除済みを除いた全てのリントルール一覧を取得する
2. 事前に定義しておいた、ゆめみの非推奨リントルール一覧を取得する
3. Google Cloud Storage API を使用して Dart / Flutter のリリース情報を取得する
4. 1, 2, 3 で取得したものを組み合わせてリントルールセットを作成する

ちなみに [リントルールの情報元] は次のような JSON ファイルで、`sinceDartSdk` を見るとリントルールが追加された Dart バージョンが分かります。

```jsonc:rules.json
[
  {
    "name": "always_use_package_imports",
    "description": "Avoid relative imports for files in `lib/`.",
    "group": "errors",
    "state": "stable",
    "incompatible": [
      "prefer_relative_imports"
    ],
    "sets": [],
    "fixStatus": "hasFix",
    "details": "**DO** avoid relative imports for files in `lib/`.\n\nWhen mixing relative and absolute imports it's possible to create confusion\nwhere the same member gets imported in two different ways. One way to avoid\nthat is to ensure you consistently use absolute imports for files within the\n`lib/` directory.\n\nThis is the opposite of 'prefer_relative_imports'.\n\nYou can also use 'avoid_relative_lib_imports' to disallow relative imports of\nfiles within `lib/` directory outside of it (for example `test/`).\n\n**BAD:**\n```dart\nimport 'baz.dart';\n\nimport 'src/bag.dart'\n\nimport '../lib/baz.dart';\n\n...\n```\n\n**GOOD:**\n```dart\nimport 'package:foo/bar.dart';\n\nimport 'package:foo/baz.dart';\n\nimport 'package:foo/src/baz.dart';\n...\n```\n\n",
    "sinceDartSdk": "2.10.0"
  },
  //...
]
```

さらに詳しく知りたい方は GitHub をご覧ください。

https://github.com/yumemi-inc/flutter-yumemi-lints/tree/fb110b32c531003f7745c850cb848d344aa8a04e/tools/update_lint_rules

また、Dart と Flutter はだいたい次のようなリリースサイクルのようですので、毎週金曜日 9:00 に起動する GitHub Workflow を作成して、そこで作成した Dart CLI を実行してリントルールセットを自動生成してプルリクエストを提出するようにしています。

## 今後の展望

ルールセットを自動生成してプルリクエストを提出するところまでは自動化できましたが、まだまだ改善点はたくさんあります。

### プルリクエストの確認作業の自動化

現時点では、提出されたプルリクエストを自動で確認する仕組みを導入できていません。

そのため、本来削減できるはずの時間をレビューに費やしてしまっています。

まずは、自動生成されたリントルールセットが以前の Dart / Flutter バージョンのルールセットと同じものかどうかを確認して、同じものであれば Approve して、違うものであれば開発メンバーがレビューを行い Approve するようにしようと考えています。

https://github.com/yumemi-inc/flutter-yumemi-lints/issues/68

### リントルールの情報元の不具合を柔軟に吸収する仕組み

`avoid_unstable_final_fields` というリントルールが Dart 3.1 のリントルールとして誤って登録されてしまったことがありました。

https://dart.dev/tools/linter-rules/avoid_unstable_final_fields

しかし、このリントルールは Dart 3.3 で追加予定のもので、Dart 3.1 では利用できません。

https://github.com/dart-lang/sdk/issues/53852

そのため、次のコミットで Dart 3.3 で追加予定として登録するように修正されました。

https://github.com/dart-lang/sdk/commit/11762189a1e78698bf0637c157bf6074f77c54a5

今回は比較的早く Dart の開発チームに対応していただけましたが、もし対応していただけない状況が長く続いてしまった場合、その間、リントルールの自動生成が正しく行えなくなってしまいます。

そのため、リントルールの情報元の不具合を柔軟に吸収できるように、不具合の検知をして、リントルールの情報を上書きできるような仕組みが必要となります。

どのようにするかは模索中ですので、何か良い方法があればこの記事か次の Issue などで教えていただけると幸いです。

https://github.com/yumemi-inc/flutter-yumemi-lints/issues/65

### その他

- プロジェクトの Dart / Flutter バージョンに適したリントルールセット選択機能の追加
- 自動生成用プロジェクトのテストコードの充実
- リントルールの細かな見直し
    - `invalid_use_of_visible_for_testing_member: error` の設定など

## おわりに

第一弾として [yumemi_lints] を公開しましたが、今後もゆめみとして OSS を開発していく予定です。

Flutter コミュニティ発展のために少しでも貢献していけるように活動を続けてまいりますので、応援していただけると幸いです。

<!-- Links -->

[yumemi_lints]: https://pub.dev/packages/yumemi_lints

[リントルールの情報元]: https://github.com/dart-lang/sdk/blob/80ad96837c207bb1d74c8c54e7387b9e6b7093df/pkg/linter/tool/machine/rules.json
