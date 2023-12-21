---
title: arb ファイルの分割について
tags:
  - Flutter
  - arb
private: false
updated_at: '2023-12-22T01:06:01+09:00'
id: ca9fddf2b977400c6259
organization_url_name: yumemi
slide: false
ignorePublish: false
---

## はじめに

Flutter での多言語対応は、公式ドキュメントで紹介されている arb ファイルを使った方法が一般的だと思います。

https://docs.flutter.dev/ui/accessibility-and-internationalization/internationalization

しかし、アプリケーションの規模が大きくなるにつれて arb ファイルが肥大化していき、管理が難しくなってしまいます。

そこで、管理しやすくするために arb ファイルを分割したくなりますが、残念ながら現状はサポートされていません。

:::note info
2022 年 7 月 7 日に機能リクエストの Issue が作成され、2023 年 10 月 26 日にプルリクエストが提出されているため、近いうちにサポートされるかもしれません。

- https://github.com/flutter/flutter/issues/107157
- https://github.com/flutter/flutter/pull/137307
:::

slang のようなパッケージを利用することで課題を解決することもできますが、すでに arb ファイルを使っている場合は学習コストも考えると少し敷居が高く感じてしまうかもしれません。

https://pub.dev/packages/slang

では、arb ファイルを分割して管理する方法はないのでしょうか。

いえ、実はあります。
マルチパッケージ構成にして、それぞれのパッケージで arb ファイルを使用することで、分割して管理することができます。

## マルチパッケージ構成で arb ファイルの分割

最終的なサンプルコードを置いておきます。

https://github.com/blendthink/arb-demo

### マルチパッケージ構成

次のようなマルチパッケージ構成で進めていきます。

```text
.
├── app # アプリケーション本体
└── packages
    ├── bar
    └── foo
```

Melos を使用すると、簡単にモノレポ形式のプロジェクトを管理することができるためオススメです。

https://pub.dev/packages/melos

### arb ファイルの分割

公式ドキュメントに従って、多言語対応の下準備を行っていきます。

まずは、foo・bar パッケージの pubspec.yaml に依存関係を追加して、generate を有効にします。

```yaml:packages/xxx/pubspec.yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
  intl: ^0.18.1
  
flutter:
  generate: true
```

次に、それぞれのルートディレクトリに中身が空の l10n.yaml を作成しておきます。

```yaml:packages/xxx/l10n.yaml
```

lib/l10n ディレクトリを作成し、arb ファイルを配置します。

```arb:packages/foo/lib/l10n/app_en.arb
{
  "foo": "foo"
}
```

```arb:packages/foo/lib/l10n/app_ja.arb
{
  "foo": "ふー"
}
```

```arb:packages/bar/lib/l10n/app_en.arb
{
  "bar": "bar"
}
```

```arb:packages/bar/lib/l10n/app_ja.arb
{
  "bar": "ばー"
}
```

それぞれのパッケージに Widget を作成します。

```dart:packages/foo/lib/foo.dart
library foo;

import 'package:flutter/material.dart';
import 'package:flutter_gen/gen_l10n/app_localizations.dart';

class FooWidget extends StatelessWidget {
  const FooWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final l = AppLocalizations.of(context)!;
    return Text(l.foo);
  }
}
```

```dart:packages/bar/lib/bar.dart
library bar;

import 'package:flutter/material.dart';
import 'package:flutter_gen/gen_l10n/app_localizations.dart';

class BarWidget extends StatelessWidget {
  const BarWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final l = AppLocalizations.of(context)!;
    return Text(l.bar);
  }
}
```

最後に、アプリケーション本体で Widget を使用します。

```dart:app/lib/main.dart
import 'package:bar/bar.dart';
import 'package:flutter/material.dart';
import 'package:foo/foo.dart';

void main() {
  runApp(const MainApp());
}

class MainApp extends StatelessWidget {
  const MainApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      home: Scaffold(
        body: Center(
          child: Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              FooWidget(),
              BarWidget(),
            ],
          ),
        ),
      ),
    );
  }
}
```

これで、対応完了!!、、かと思いきや、次のようなエラーが発生します。

```text
Launching lib/main.dart on iPhone 14 Pro in debug mode...
Running Xcode build...
Xcode build done.                                            9.5s
Failed to build iOS app
Error (Xcode): Invalid depfile: /xxx/arb-demo/app/.dart_tool/flutter_build/1d71dacafbf048692cb1384285002b76/kernel_snapshot.d

Could not build the application for the simulator.
Error launching application on iPhone 14 Pro.
```

このエラーは、foo・bar パッケージそれぞれで自動生成された AppLocalizations クラスを利用できないというものです。

どうやら .dart_tool へ自動生成されたクラスを使用している場合は、パッケージ間での参照ができないようです。

従って、.dart_tool ではなくパッケージ内に自動生成されたクラスを配置する必要があります。

foo・bar パッケージの l10n.yaml を次のように修正して

```diff:packages/xxx/l10n.yaml
+ output-dir: lib/gen/l10n
+ synthetic-package: false
```

foo・bar パッケージの pubspec.yaml の generate を無効にします。

```diff
- flutter:
-   generate: true
```

foo・bar パッケージの Widget の import を次のように修正します。

```diff:packages/xxx/lib/xxx.dart
- import 'package:flutter_gen/gen_l10n/app_localizations.dart';
+ import 'package:xxx/gen/l10n/app_localizations.dart';
```

これで、対応完了!!、、、、、、、、かと思いきや、次のようなエラーが発生します。

```text
======== Exception caught by widgets library =======================================================
The following _TypeError was thrown building FooWidget(dirty, dependencies: [_LocalizationsScope-[GlobalKey#ea537]]):
Null check operator used on a null value
...
...
...
======== Exception caught by widgets library =======================================================
The following _TypeError was thrown building BarWidget(dirty, dependencies: [_LocalizationsScope-[GlobalKey#ea537]]):
Null check operator used on a null value
...
...
...
```

これは、foo・bar パッケージそれぞれの `AppLocalizations.of(context)` が null を返しているためです。

自動生成した `AppLocalizations` を使用するためには `MaterialApp` の `localizationsDelegates` と `supportedLocales` に設定を追加する必要があります。

そのために、foo・bar パッケージそれぞれの `AppLocalizations` をアプリケーション本体で使用できるように l10n.dart というファイルを作成して export します。

```dart:packages/xxx/l10n.dart
export 'package:foo/gen/l10n/app_localizations.dart' show AppLocalizations;
```

アプリケーション本体を次のように修正します。

```diff:app/lib/main.dart
  import 'package:bar/bar.dart';
+ import 'package:bar/l10n.dart' as bar_l10n;
  import 'package:flutter/material.dart';
  import 'package:foo/foo.dart';
+ import 'package:foo/l10n.dart' as foo_l10n;
  
  void main() {
    runApp(const MainApp());
@@ -12,15 +14,32 @@ class MainApp extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
      return const MaterialApp(
-       home: Scaffold(
-         body: Center(
-           child: Row(
-             mainAxisAlignment: MainAxisAlignment.center,
-             children: [
-               FooWidget(),
-               BarWidget(),
-             ],
-           ),
+       localizationsDelegates: [
+         ...bar_l10n.AppLocalizations.localizationsDelegates,
+         ...foo_l10n.AppLocalizations.localizationsDelegates,
+       ],
+       supportedLocales: [
+         ...bar_l10n.AppLocalizations.supportedLocales,
+         ...foo_l10n.AppLocalizations.supportedLocales,
+       ],
+       home: HomeWidget(),
+     );
+   }
+ }
+ 
+ class HomeWidget extends StatelessWidget {
+   const HomeWidget({super.key});
+ 
+   @override
+   Widget build(BuildContext context) {
+     return const Scaffold(
+       body: Center(
+         child: Row(
+           mainAxisAlignment: MainAxisAlignment.center,
+           children: [
+             FooWidget(),
+             BarWidget(),
+           ],
          ),
        ),
      );
```

すると、無事に次のように表示されました :tada:

| 英語 | 日本語 |
| :-: | :-: |
| <img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/a8649c4e-53bb-618b-a29a-42f6e1cec8f4.png" width="320" alt="英語の完成時のスクリーンショット"> |<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/f24b5d03-9ab6-35f1-265c-0f4dd4034283.png" width="320" alt="日本語の完成時のスクリーンショット"> |

## おわりに

いかがでしたでしょうか。

マルチパッケージ構成で arb ファイルを分割する方法を紹介しました。 この方法を使うことで arb ファイルの肥大化を防ぐだけでなく、より適切な範囲で文言の管理を行うことができます。

少し設定の手間は必要ですが、個人的におすすめの方法ですので、ぜひご活用していただければ幸いです。
