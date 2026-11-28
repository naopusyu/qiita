---
title: git reflogを使って消えたコミットを復元する
tags:
  - 'Git'
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

Gitを操作していたら、コミットしたはずの内容が消えていた。
そこで変更履歴からコミットを復元させる方法をメモしておく。

## 方法

`git reflog`コマンドを使う。

https://git-scm.com/docs/git-reflog

:::note info
いわゆるHEADやブランチなどの参照の変更履歴を記録したものです。
:::

`git reflog`コマンドを実行すると次のような一覧が出てくる。

```bash
$ git reflog
a123456 HEAD@{0}: commit: xxxxxxxxxx
b123456 HEAD@{1}: commit: xxxxxxxxxx
c123456 HEAD@{2}: commit: xxxxxxxxxx
d123456 HEAD@{3}: commit: xxxxxxxxxx
e123456 HEAD@{4}: commit: xxxxxxxxxx
f123456 HEAD@{5}: commit: xxxxxxxxxx
```

:::note info
reflogには保存期間があるため、実行するタイミングによっては、復元したいコミットが一覧に出ない場合もある。
:::

コミットハッシュはこれでわかったので、次の操作でコミットを復元する。

- 新しいブランチを作る

```bash
git branch <ブランチ名> <コミットハッシュ>
```

現在のブランチや作業ディレクトリに影響を与えない安全な方法。


- cherry-pickで取り込む

```bash
git cherry-pick <コミットハッシュ>
```

現在のブランチに復元するコミットの内容を取り込む。
こちらの場合、新しいコミットとして取り込まれる。

これで復元は完了です。
あとは必要に応じてmain/masterにマージする。

