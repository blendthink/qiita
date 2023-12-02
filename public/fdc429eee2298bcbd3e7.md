---
title: Mac で GitHub に SSH 接続する方法（Fork を利用）
tags:
  - Mac
  - Git
  - GitHub
  - SSH
  - fork
private: false
updated_at: '2020-07-26T03:04:22+09:00'
id: fdc429eee2298bcbd3e7
organization_url_name: yumemi
slide: false
ignorePublish: false
---
# はじめに

GitHub に SSH 接続してたけど
PC を変えた時にどうやって設定してたか忘れてしまった...

そんな方のため（主に自分のため笑）に記事として残すことにしました :sunglasses: 

## 環境

- macOS Catalina バージョン 10.15.6
- git version 2.24.3 (Apple Git-128)
- Fork Version 1.0.96


## 前提条件

- GitHub のアカウントがある
- GitHub に private リポジトリがある
- Git が使用できる状態になっている
- [Fork](https://git-fork.com/) という Git クライアントをインストール済み

# 接続するまで

[1. 認証用ディレクトリを作成](https://qiita.com/blendthink/items/fdc429eee2298bcbd3e7#1-%E8%AA%8D%E8%A8%BC%E7%94%A8%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%88%E3%83%AA%E3%82%92%E4%BD%9C%E6%88%90)
[2. 公開鍵と秘密鍵を作成](https://qiita.com/blendthink/items/fdc429eee2298bcbd3e7#2-%E5%85%AC%E9%96%8B%E9%8D%B5%E3%81%A8%E7%A7%98%E5%AF%86%E9%8D%B5%E3%82%92%E4%BD%9C%E6%88%90)
[3. 接続設定を追加](https://qiita.com/blendthink/items/fdc429eee2298bcbd3e7#3-%E6%8E%A5%E7%B6%9A%E8%A8%AD%E5%AE%9A%E3%82%92%E8%BF%BD%E5%8A%A0)
[4. GitHub に公開鍵を登録する](https://qiita.com/blendthink/items/fdc429eee2298bcbd3e7#4-github-%E3%81%AB%E5%85%AC%E9%96%8B%E9%8D%B5%E3%82%92%E7%99%BB%E9%8C%B2%E3%81%99%E3%82%8B)
[5. Fork の設定](https://qiita.com/blendthink/items/fdc429eee2298bcbd3e7#5-fork-%E3%81%AE%E8%A8%AD%E5%AE%9A)
[6. 接続できるか確認](https://qiita.com/blendthink/items/fdc429eee2298bcbd3e7#6-%E6%8E%A5%E7%B6%9A%E3%81%A7%E3%81%8D%E3%82%8B%E3%81%8B%E7%A2%BA%E8%AA%8D)

## 1. 認証用ディレクトリを作成

```
# 現在のユーザーで作成
$ mkdir ~/.ssh

# 所有者読み・書き・実行権限を付与
$ chmod 700 ~/.ssh
```

## 2. 公開鍵と秘密鍵を作成

```
# 認証用ディレクトリに移動
$ cd ~/.ssh

# 認証鍵を作成
#
# 以下の３つについて聞かれるため入力する
# 1. 鍵情報を保存するファイルの名前（今回は id_github_rsa ）
# 2. パスフレーズ
# 3. パスフレーズ（確認用）
$ ssh-keygen -t rsa

# 認証鍵に所有者読み込み権限を付与
$ chmod 400 ~/.ssh/id_github_rsa
$ chmod 400 ~/.ssh/id_github_rsa.pub

# ssh-agent に秘密鍵を登録
$ ssh-add ~/.ssh/id_github_rsa
```

こんな感じで生成できたかと思います。
ちなみに Finder で「 shift + command + . 」とすると隠しファイルが見れるようになります :sunglasses: 
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/2266da96-a1a0-36c6-b623-f32279c54847.png" width=480>

## 3. 接続設定を追加

```
# 認証用の設定ファイルを作成
$ touch ~/.ssh/config

# 所有者読み・書き権限を付与
$ chmod 600 ~/.ssh/config

# 編集を開始
$ vi ~/.ssh/config

#
# 編集終わったら保存して次へ
#

# 内容を確認
$ cat ~/.ssh/config
```

中身は下記のようにします

```text
Host github
 HostName github.com
 IdentityFile ~/.ssh/id_github_rsa
 User git
 Port 22
```

## 4. GitHub に公開鍵を登録する

Terminal の以下のコマンドで、公開鍵の中身をコピーして

```
$ pbcopy < ~/.ssh/id_github_rsa.pub
```


GitHub の [SSH キーの登録画面](https://github.com/settings/ssh/new) に貼り付けて「Add SSH key」を押して、SSH キーを登録する

<img width="480" alt="貼り付けた画像_2020_07_26_0_50.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/5bf88699-8906-23fb-b486-7d60047d34d1.png">


## 5. Fork の設定

GitHub にログインして SSH key を設定する

| ログイン前 | ログイン後 | SSH key 設定後 |
| --- | --- | --- |
| <img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/793b310a-1b23-660b-15b3-399700906054.png" width="320"> | <img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/58bfdd43-16f7-6c8e-2f48-6ac62199847e.png" width="320"> | <img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/e113ae78-c906-448b-3b01-3f10ba6128a1.png" width="320"> |

## 6. 接続できるか確認

GitHub の private リポジトリの SSH 用のパスをコピーして

<img width="320" alt="貼り付けた画像_2020_07_26_1_09.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/40239b36-9ef1-aa43-4642-7b75fd37fdd5.png">

Fork でクローン

<img width="480" alt="貼り付けた画像_2020_07_26_2_27.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/165251/49f898da-3e7c-c2a0-9a94-151ef722d262.png">

無事にクローンできたら :ok_hand: 

以下のコマンドを使って確認する方法もあります

```
# 公開鍵が登録されたか確認
$ ssh -T git@github.com

# 登録したホスト名でも接続できるか確認
$ ssh github
```

# 接続できなかったら

この記事にコメントなり Twitter の DM なり、気軽に質問していただけたらと思います :hand_splayed: 
できれば、知見を集約するという意味で、この記事にコメントしていただけると幸いです :relieved:

## 参考記事

- [Error: Permission denied (publickey)](https://docs.github.com/ja/github/authenticating-to-github/error-permission-denied-publickey)

# おわりに

今回は Mac の Terminal で認証鍵を作成したり、Web で GitHub に公開鍵を登録したりしていましたが、実はこれらの作業は Fork で行えたりします。やりやすい方でやってもらえればいいと思います！

[Fork](https://git-fork.com/) おすすめなのでみなさん使ってみてください :hand_splayed: 
