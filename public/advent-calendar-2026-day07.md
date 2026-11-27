---
title: C#をインストールしてHello Worldを出してみた
tags:
  - 'C#'
  - 'HelloWorld'
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

C#のインストールから**Hello World**を出すところまでをやってみます。

## 環境

- Mac

## インストール

Homebrewを使って、.NET SDKをインストールする。

```bash
$ brew install --cask dotnet-sdk
```

インストールが終わったら、バージョンを確認する。

```bash
$ dotnet --version
11.0.100
```

## Hello Worldを出してみる

HelloWorld.csを作成し、次の内容を書き込む。

```csharp
Console.WriteLine("Hello, World!");
```

保存後、`dotnet run`を実行します。

:::note info
.NET 10で導入された **ファイルベースアプリ（file-based apps）** 機能のおかげでプロジェクトを作成しないでも`.cs`ファイルから直接実行できるようになった。
:::

```bash
$ dotnet run HelloWorld.cs
Hello, World!
```

:::note info
初回はビルドが行われるため、少し時間がかかります。
2回目以降は、ファイルの変更がなければビルド済みの成果物を利用するため、実行が早くなります。
:::
