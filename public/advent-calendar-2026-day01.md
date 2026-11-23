---
title: Qiitaの記事をGitHubで管理する
tags:
  - 'Qiita'
  - 'GitHub'
private: true
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: true
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---

## はじめに

せっかくQiitaの書くなら、GitHubで管理したい！

そんな気持ちになりますよね！

その思い、なんと、Qiita CLIを使えば出来るです！

そんなわけで、これからの記事執筆のための環境を構築します！

:::note info
もちろんこの記事はGitHubで管理されています。
:::

## 環境構築

Qiita CLIのREADMEに書いてある手順通りに進めていけば問題なかったです。

https://github.com/increments/qiita-cli

大事なところは次の通り。

- Node.js が 22.22.1 以上が必要
- Qiitaの個人用トークンを発行（設定 > アプリケーション > 新しくトークンを発行する）

発行したトークンを使って、Qiita CLIのログインを行う。

```bash
npx qiita login
```

これでQiitaのアカウントと紐付きが出来たので、記事の登録、更新が出来るようになりました。

つぎはGitHubの設定です。

こちらもQiitaの公式からの手順通りに進めていけば問題なしです。

https://qiita.com/Qiita/items/32c79014509987541130

これで、Qiita CLIとGitHubの準備が出来た状態です。

## 記事の作成

記事はQiita CLIを使って作成します。

```bash
npx qiita new
```

コマンドを実行するとMarkdownファイルが作成されます。

あとはMarkdownファイルを編集すればokです。

ファイル上部の`---`に挟まれている部分が記事の設定です。

- タイトル(title)やタグ(tags)の指定はここで行う
- 記事の公開(false)/非公開(true)はignorePublishを使う
- 記事を限定するならprivateにtrueを指定する

:::note warn
ignorePublishは記事を公開できるって時にfalseにしましょう。
:::

Markdown記法はこちらの記事を参考にしています。

https://qiita.com/Qiita/items/c686397e4a0f4f11683d

次のコマンドを実行するとブラウザで記事を確認しながら執筆できる。

```bash
npx qiita preview
```

## 記事の投稿

つぎのコマンドを使って、記事の投稿を行う。

```bash
npx qiita publish 投稿したいMarkdownファイル指定
```

で良さそうだけど、
今回は記事をGitHubで管理するのでコマンドは使わないで記事のMarkdownファイルをcommit、pushします。
そうすると、GitHubの設定が上手く出来ていれば、pushをトリガーにGitHub Actionsが動き記事が公開されます。

## まとめ

これで記事の管理はGitHubで行えるようになりました。
記事の執筆も好きなエディタで書けるようになりましたね。
