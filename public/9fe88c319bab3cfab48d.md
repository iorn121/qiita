---
title: 【備忘録】Rust × WebAssembly 環境構築【project作成からnpm run startまで】
tags:
  - Rust
  - npm
  - WebAssembly
private: false
updated_at: '2022-08-05T09:33:53+09:00'
id: 9fe88c319bab3cfab48d
organization_url_name: nnn-school
slide: false
ignorePublish: false
---
## 背景
簡単に Rust × WebAssembly 環境を作りたいのに、毎回チュートリアルを探すことが多かったです。
あまり日本語での情報も少ないので、備忘録として残しておきます。

情報は主に[こちらのチュートリアル](https://rustwasm.github.io/docs/book/introduction.html)を参考にしています。

## 前提
* Rust ツールチェーンがインストールされている （ rustup, rustc, cargo ）
* npm がインストールされている

## 必要なツールのインストール

### wasm-pack のインストール

wasm-pack によって Rust で生成された WebAssembly をビルド、テスト、公開することができます。
下記のコマンドを実行してインストールしましょう。

```bash
curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
```


[こちら](https://rustwasm.github.io/wasm-pack/book/)がドキュメントになります。

### cargo-generate のインストール

既存の git リポジトリをテンプレートとして活用して、新しい Rust プロジェクトを立ち上げて実行できます。

```bash
cargo install cargo-generate
```

[こちら](https://github.com/cargo-generate/cargo-generate)がドキュメントになります。


### npm の最新版をインストール

JavaScript バンドラーと開発サーバーを最新版にしておきます。
（冒頭で紹介したチュートリアルの最後に、コンパイル.wasmしたものをnpmレジストリに公開する工程があります）

```bash
npm install npm@latest -g
```

## Rust プロジェクトの作成

下記のコマンドで雛形のリポジトリからプロジェクトを作成します。
```bash
cargo generate --git https://github.com/rustwasm/wasm-pack-template
```

プロジェクト名を要求されるので、適した名前をつけてあげましょう。
スネークケースかケバブケースにすると良いです。

ここでは便宜上、`hello_world`というプロジェクト名とします。

`cd hello_world`でプロジェクトのディレクトリ内に移動しておきましょう。



## wasm-packでビルドする

最初は　`Hello, ~~!`とアラートを出すプログラムが書かれています。


プロジェクトディレクトリ内で下記のコマンドでビルドを実行します。

```bash
wasm-pack build
```

デフォルトでは`--release`オプションで最適化されたビルドを行います。
作成されたファイルは`pkg`フォルダに格納されています。
詳細は[こちらのドキュメント](https://rustwasm.github.io/docs/wasm-pack/commands/build.html)を参照してください。

## npmでモジュールを導入する

`npm init`でパッケージマネージャを初期化します。
ここでは`www`という名前にしています。

```bash
npm init wasm-app www
```

作成された`package.json`ファイルの`devDependencies`に下記を追記します。
ここではモジュール名を`wasm-hello-world`としています。

```json:package.json
{
  "devDependencies": {
    "wasm-hello-world": "file:../pkg",
  }
}
```
追加したモジュールを`index.js`内でimportします。
```javascript:index.js
import * as wasm from "wasm-blur-detection";

wasm.greet();

```

`www`ディレクトリ内で`npm install`をします。

```bash
cd www
npm install
```

## 実行してみる

```bash
npm run start
```

`http://localhost:8080/`にアクセスすると、`Hello, wasm-hello-world!`とアラートが出るはずです。

この時、Node.js v17 以降では`npm run start`が実行できないようです。
その場合は、下記のコマンドで環境変数を変更して再度実行するとうまくいきます。
```bash
export NODE_OPTIONS=--openssl-legacy-provider
```
詳細は[こちらの記事](https://qiita.com/iorn121/items/1779d07a3f699b655991)を参考にしてください。

## まとめ

以上で、簡単に Rust × WebAssembly の環境構築をして、hello, worldまで済ませることができました。

紹介したチュートリアルではライフゲームを実装しており、テストの方法やボタンを用いたインタラクティブな実装まで説明があります。
とても参考になるので、ぜひご覧ください。
