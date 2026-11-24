---
title: git worktreeの使い方を試してみる
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

AIコーディングエージェントの普及によって**git worktree**を利用する機会が増えました。

ただ、どのような機能なのか、いまいち理解していない状態なので、基本的な使い方を試してみます。

## git worktreeとは

1つのGitリポジトリで複数の作業ディレクトリを管理できる機能です。

通常はブランチを切り替えながら作業しますが、**git worktree**を使うことで異なるブランチを別々のディレクトリに展開して並行して作業できる。

https://git-scm.com/docs/git-worktree

この機能は2015年にリリースされたGit 2.5で追加されています。

https://www.infoq.com/jp/news/2015/08/git-25-released/

https://www.atlassian.com/ja/blog/cool-features-git-2-x

https://github.blog/open-source/git/git-2-5-including-multiple-worktrees-and-triangular-workflows/

## git worktreeを使ってみる

実際にgit worktreeを使って、複数のブランチで並行して作業できることを確認してみます。

まずはワークツリーを作成します。

```bash
git worktree add -b feature/sample ../sample
```

上記の場合だと、現在のHEADを起点に`feature/sample`ブランチを作って、作業するディレクトリとして`../sample`を用意する。

既存のブランチを指定してワークツリーを作る場合は`-b`は不要です。

```bash
git worktree add ../sample feature/sample 
```

作成したワークツリーは以下のコマンドで確認が取れる。

```bash
git worktree list
```

:::note info
同じブランチを複数のワークツリーで同時にチェックアウトすることはできない。
:::

実際の修正などは作成した作業ディレクトリに移動して行う。

作業が終わったら、ワークツリーを削除する。

```bash
git worktree remove ../sample
```

ワークツリーを削除しても、ブランチは残ります。
