---
title: GitLab CIのtriggerを使って別のパイプラインを起動してみる
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

GitLab CIにはパイプラインから別のパイプラインを起動できるtriggerという仕組みが用意されている。

今回はtriggerを使った場合、どのように動くのか確認します。

## triggerとは

https://docs.gitlab.com/ci/yaml/#trigger

通常、pushやマージリクエストを起点にパイプラインが実行されるが、triggerを使えばパイプラインから別のパイプラインを動かすことができる。

このtriggerには大きく分けて2種類があります。

- multi-project-pipelines:別プロジェクトのパイプラインを起動
- parent-child-pipelines:同じプロジェクトで子パイプラインを起動

今回はparent-child-pipelinesを使います。

## 子パイプラインを起動してみる

次の2つのファイルを用意する。

```yaml:.gitlab-ci.yml
stages:
  - test

trigger-test:
  stage: test
  trigger:
    include:
      - local: child.yml
```

```yaml:child.yml
stages:
  - test

test:
  stage: test
  script:
    - echo "Child pipeline started"
```

この状態でパイプラインを起動すると、trigger-testジョブから子パイプラインが起動する。

https://gitlab.com/naopusyu/gitlib-ci-trigger/-/pipelines/3099106231

## 子パイプラインが失敗した場合の動き

子パイプラインが失敗すると、親パイプラインがどのように動くのか確認するために先程のchild.ymlを意図的に失敗させてみます。

```diff_yaml:child.yml
stages:
  - test

test:
  stage: test
  script:
    - echo "Child pipeline started"
+   - exit 1
```

この状態でパイプラインを動かすと子パイプラインは失敗していますが、親パイプラインは成功しています。

https://gitlab.com/naopusyu/gitlib-ci-trigger/-/pipelines/3099119755

つまり、親パイプラインは子パイプラインを実行した時点で処理は成功とみなし、子パイプラインの結果は見ないようです。

では、子パイプラインの結果を待ちたいケースはどうしたらいいのか？

それは**strategy: mirror**を指定することで実現できるようです。

https://docs.gitlab.com/ci/yaml/#triggerstrategy

:::note info
strategy: mirrorはGitLab 18.2で導入された機能です。
:::

親パイプラインのtrigger-testジョブに指定して、パイプラインを実行してみます。

```diff_yaml:.gitlab-ci.yml
stages:
  - test

trigger-test:
  stage: test
  trigger:
    include:
      - local: child.yml
+   strategy: mirror
```

この状態だと、子パイプラインの失敗が親パイプラインにも反映した状態になりました。

https://gitlab.com/naopusyu/gitlib-ci-trigger/-/pipelines/3099138836

## まとめ

今回はGitLab CIのtriggerについて確認してみました。

ジョブの結果は気にしないが、非同期でジョブを動かしておきたいケースで使えそうな機能な気がしています。
