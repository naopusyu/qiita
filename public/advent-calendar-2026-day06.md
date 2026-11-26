---
title: Composerの設定ファイルは切り替えられるらしいので試してみた
tags:
  - 'PHP'
  - 'Composer'
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

Composerの設定ファイルは通常、composer.jsonの1つだけで管理します。

ただ、Composerには設定ファイルを切り替える仕組みが用意されています。

今回は複数のcomposer.jsonを用意して、それぞれの設定でパッケージのインストールを試してみたいと思います。

## 環境

- Composer 2.11.3

## 切り替えの仕組み

この記事で一番大事な仕組みの部分ですが、以下のドキュメントに書かれている環境変数の**COMPOSER**を使います。

https://getcomposer.org/doc/03-cli.md#composer

通常は次のコマンドでcomposer.jsonを読み込みます。

```bash
composer install
```

このCOMPOSERに別のcomposer.json（例えば、composer-test.json）を指定することで切り替えが可能になるわけです。

```bash
COMPOSER=composer-test.json composer install
```

:::note info
composer.lockもcomposer.jsonに合わせて別名で生成されます。
上記のcomposer-test.jsonなら、composer-test.lockで生成される。
:::

## お試し

今回はPHP8.3とPHP8.6の2つの環境を用意して試します。

```bash
$ tree
.
├── compose.yaml
├── composer-8.3.json
├── composer-8.6.json
└── Dockerfile
```

それぞれの内容は以下の通り。

```json:composer-8.3.json
{
    "require": {
        "php": "8.3.*"
    }
}
```

```json:composer-8.6.json
{
    "require": {
        "php": "8.6.*"
    }
}
```

```yaml:compose.yaml
services:
  php86:
    build:
      context: .
      args:
        PHP_VERSION: "8.6"
    working_dir: /app
    volumes:
      - ./:/app
    tty: true

  php83:
    build:
      context: .
      args:
        PHP_VERSION: "8.3"
    working_dir: /app
    volumes:
      - ./:/app
    tty: true
```

```Dockerfile
ARG PHP_VERSION=8.6

FROM php:${PHP_VERSION}-cli

RUN apt-get update \
    && apt-get install -y unzip \
    && rm -rf /var/lib/apt/lists/*

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /app
```

インストールするパッケージは私が作成したものを使おうと思います。

https://github.com/naopusyu/phpcs-custom-rules

- 8.3 でインストール

```bash
$ docker compose exec -e COMPOSER=composer-8.3.json php83 composer require naopusyu/phpcs-custom-rules
Cannot use naopusyu/phpcs-custom-rules's latest version v1.0.0 as it requires php >=8.4 which is not satisfied by your platform.
composer-8.3.json has been updated
Running composer update naopusyu/phpcs-custom-rules
Loading composer repositories with package information
Updating dependencies
Lock file operations: 2 installs, 0 updates, 0 removals
  - Locking naopusyu/phpcs-custom-rules (0.6.0)
  - Locking squizlabs/php_codesniffer (3.13.6)
Writing lock file
Installing dependencies from lock file (including require-dev)
Package operations: 2 installs, 0 updates, 0 removals
  - Installing squizlabs/php_codesniffer (3.13.6): Extracting archive
  - Installing naopusyu/phpcs-custom-rules (0.6.0): Extracting archive
Generating autoload files
1 package you are using is looking for funding.
Use the `composer fund` command to find out more!
No security vulnerability advisories found.
Using version ^0.6.0 for naopusyu/phpcs-custom-rules
```

最初に警告が出ているのは、

> パッケージのバージョンを指定していないため、Composerは最新版をインストールしようとする。
> でも、最新版が要求するPHPのバージョンを満たしていないため、現在の環境で依存関係の要件を満たすバージョンをインストールする。

こういうことみたいです。

- 8.6 でインストール

```bash
$ docker compose exec -e COMPOSER=composer-8.6.json php86 composer require naopusyu/phpcs-custom-rules
composer-8.6.json has been updated
Running composer update naopusyu/phpcs-custom-rules
Loading composer repositories with package information
Updating dependencies
Lock file operations: 2 installs, 0 updates, 0 removals
  - Locking naopusyu/phpcs-custom-rules (v1.0.0)
  - Locking squizlabs/php_codesniffer (4.0.4)
Writing lock file
Installing dependencies from lock file (including require-dev)
Package operations: 2 installs, 0 updates, 0 removals
  - Installing squizlabs/php_codesniffer (4.0.4): Extracting archive
  - Installing naopusyu/phpcs-custom-rules (v1.0.0): Extracting archive
1 package suggestions were added by new dependencies, use `composer suggest` to see details.
Generating autoload files
1 package you are using is looking for funding.
Use the `composer fund` command to find out more!
No security vulnerability advisories found.
Using version ^1.0 for naopusyu/phpcs-custom-rules
```

逆にPHP8.6の環境では、警告は出ずに最新のバージョンをインストールしています。

ここまでの操作を行うとインストール後のディレクトリ構成は次のようになる。

```bash
.
├── compose.yaml
├── composer-8.3.json
├── composer-8.3.lock
├── composer-8.6.json
├── composer-8.6.lock
├── Dockerfile
└── vendor
```

それぞれの設定ファイルのlockファイルが作成されました。
これでPHPのバージョンごとでインストールするパッケージを分けることが可能であることがわかります。
ただし、vendorディレクトリは1つだけなのでインストールするパッケージは共存できないです。

もし、インストールするパッケージも分けたい場合はcomposer.jsonにvendor-dirの指定が必要になってくる。
この指定を入れることでインストールするパッケージもPHPのバージョンで分けることが可能です。

https://getcomposer.org/doc/06-config.md#vendor-dir

## まとめ

Composerの設定ファイルであるcomposer.jsonを複数用意して、切り替えれることを検証しました。
ただ、現時点でこの仕組みを使うことで得られるメリットを見出せなかったです。

