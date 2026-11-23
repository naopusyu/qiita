---
title: GitHubからパッチファイルを取得して適用する方法
tags:
  - ''
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

GitHubにあるコミット、プルリクエストの変更内容から、パッチファイルとして取得し、適用する方法をまとめた。

## GitHubからパッチファイルを取得する

GitHubのコミット、プルリクエストのURLの末尾に`.patch`を付けると取得できる。

### コミットから取得する

以下の形式で取得できる。

https://github.com/owner/repository/commit/<commit-hash>.patch

curlを利用する場合は以下のコマンドで保存できる。

```bash
curl -fL https://github.com/owner/repository/commit/<commit-hash>.patch -o fix.patch
```

### プルリクエストから取得する

プルリクエストも同じ形式で取得できる。

https://github.com/owner/repository/pull/123.patch

```bash
curl -fL https://github.com/owner/repository/pull/123.patch -o fix.patch
```

## パッチファイルを適用する

さきほど取得したパッチファイルを適用するには、対象のソースコードのルートディレクトリに移動してから、`patch`コマンドを使う。

適用できるのか、`--dry-run`を付けることで確認できる。

```bash
patch --dry-run -p1 < fix.patch
```

問題なければ適用する。

```bash
patch -p1 < fix.patch
```

:::note info
`-p1`はパッチファイルに記載されたファイルパスの先頭1階層を取り除くオプションです。
例えば、a/src/example.phpがパッチファイルに記載されていたら、src/example.phpに変更が適用されます。
:::

## 確認

Gitで管理しているファイルであれば、`git diff`を実行してパッチファイルの内容が差分で出てくれば成功です。
