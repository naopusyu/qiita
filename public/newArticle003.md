---
title: GitLab CIでGit cloneをスキップする方法
tags:
  - 'GitLab'
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

GitLab CIでリポジトリのソースコードを必要としないジョブで毎回`git clone`を行ってそれなりに時間を使っていることがあり、
大変無駄だと感じていたので、`git clone`を行わない方法はないかと調べるとありました。

環境変数の`GIT_STRATEGY`を使えばよさそうなので、ちょっと動作を確認してみました。

## GIT_STRATEGYとは

https://docs.gitlab.com/ci/runners/configure_runners/#git-strategy

ジョブが動き出した時にリポジトリのフェッチ方法を設定するための環境変数です。

指定できるのは、`clone`、`fetch`、`none`、`empty`の4つあります。

それぞれの役割は以下の通りです。

| 設定値 | 動作 |
|:-|:-|
| clone | ジョブごとに毎回リポジトリをクローンする |
| fetch | クローン済みのリポジトリを再利用し、差分のみ取得する（リポジトリがなければcloneと同じ） |
| none | クローン済みのリポジトリを再利用するが、Git操作はスキップする |
| empty | 空の状態 |

## 検証する

4つの設定が書かれた.gitlab-ci.ymlを用意してジョブを実行します。

リポジトリが存在していれば、**Hello from repository!**と出力されるようになっている。

```yaml:.gitlab-ci.yml
stages:
  - test

.test:
  stage: test
  script:
    - sh hello.sh

clone:
  extends: .test
  variables:
    GIT_STRATEGY: clone

fetch:
  extends: .test
  variables:
    GIT_STRATEGY: fetch

none:
  extends: .test
  variables:
    GIT_STRATEGY: none

empty:
  extends: .test
  variables:
    GIT_STRATEGY: empty

```

```shell:hello.sh
#!/bin/sh
echo "Hello from repository!"
```

結果はこちらです。

https://gitlab.com/naopusyu/GIT_STRATEGY/-/pipelines/3097617460

想定通り、`clone`と`fetch`はジョブが成功、`none`と`empty`は失敗でした。

## まとめ

GIT_STRATEGYを使うことで、リポジトリのソースコードの取得方法を変えることがわかりました。

ソースコードが取得不要なジョブであれば、`none`または`empty`を指定することでGit操作をスキップでき、ジョブの実行時間も短縮できそうです。
