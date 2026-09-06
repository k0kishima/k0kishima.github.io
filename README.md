# Fuji Enterprise コーポレートサイト

富士エンタープライズの公式コーポレートサイトです。

## 必要な環境

- Docker

ホスト OS に Ruby や rbenv をインストールする必要はありません。使い捨てのコンテナで Jekyll を動かします。

## ローカルでの確認（Docker / 推奨）

### 1. 依存 gem のインストール（初回のみ）

```bash
docker run --rm \
  -v "$PWD:/srv/jekyll" \
  -v jekyll-bundle:/bundle \
  -e BUNDLE_PATH=/bundle \
  jekyll/jekyll bundle install
```

gem は named volume `jekyll-bundle` に入るため、リポジトリにもホスト OS にもファイルは残りません。

### 2. ローカルサーバーの起動

```bash
docker run --rm \
  -v "$PWD:/srv/jekyll" \
  -v jekyll-bundle:/bundle \
  -e BUNDLE_PATH=/bundle \
  -p 4000:4000 \
  jekyll/jekyll bundle exec jekyll serve --host 0.0.0.0
```

ブラウザで http://localhost:4000 にアクセスすると、サイトをプレビューできます。

ファイルを編集すると自動的にリロードされます（`_config.yml` の変更のみ再起動が必要）。

### 3. サーバーの停止

`Ctrl + C` でサーバーを停止します。`--rm` を付けているのでコンテナは自動的に破棄されます。

### コマンドの注意点

省略するとエラーになるため、以下の 3 点が必要です。

- **`bundle install` を先に実行する**: `jekyll/jekyll` イメージには Jekyll 本体しか含まれておらず、`Gemfile.lock` が指定する gem は入っていないため、省略すると `Bundler::GemNotFound` になります
- **`bundle exec` を付ける**: 付けないとイメージ側の rake と `Gemfile.lock` が指定する rake のバージョンが衝突し、`Gem::LoadError` になります
- **`--host 0.0.0.0` を付ける**: Jekyll のデフォルトの `127.0.0.1` はコンテナ内の loopback を指すため、ホスト側のブラウザから接続できません

### 環境のリセット

gem の状態がおかしくなった場合は、volume を削除して手順 1 からやり直します。

```bash
docker volume rm jekyll-bundle
```

## ローカルでの確認（ホストに Ruby を入れる方法）

Docker を使わない場合の手順です。ホスト OS に rbenv と Ruby 3.3.x をインストールするため、環境を汚さない Docker 方式を推奨します。

<details>
<summary>rbenv を使ったセットアップ手順を開く</summary>

必要な環境: Ruby 3.3.x 以上 / rbenv / Bundler / Jekyll

#### 1. rbenvのインストール（未インストールの場合）

```bash
# Homebrewでrbenvをインストール
brew install rbenv ruby-build

# .zshrc または .bash_profile に追加
echo 'eval "$(rbenv init - zsh)"' >> ~/.zshrc
# bashの場合
# echo 'eval "$(rbenv init - bash)"' >> ~/.bash_profile

# シェルを再起動
exec $SHELL
```

#### 2. Ruby 3.3系の最新版をインストール

```bash
# インストール可能なRubyバージョンを確認
rbenv install -l | grep 3.3

# Ruby 3.3系の最新版をインストール（例: 3.3.10）
rbenv install 3.3.10

# プロジェクトディレクトリでRubyバージョンを設定
cd /path/to/k0kishima.github.io
rbenv local 3.3.10

# rbenvのshimを再構築（重要！）
rbenv rehash

# バージョン確認
ruby -v
# ruby 3.3.10 と表示されればOK
```

#### 3. Bundlerのインストール

```bash
gem install bundler
```

#### 4. Gemfileの作成とJekyllのインストール

このリポジトリには既にGemfileがあるので、以下のコマンドを実行：

```bash
bundle install
```

#### 5. ローカルサーバーの起動

```bash
bundle exec jekyll serve
```

ブラウザで http://localhost:4000 にアクセスすると、サイトをプレビューできます。

ファイルを編集すると自動的にリロードされます（一部のファイルは再起動が必要）。

#### 6. サーバーの停止

`Ctrl + C` でサーバーを停止します。

</details>

## ディレクトリ構成

```
.
├── _config.yml          # Jekyll設定ファイル
├── _layouts/            # レイアウトテンプレート
│   └── default.html
├── _includes/           # 共通パーツ
│   ├── header.html      # ヘッダー
│   └── footer.html      # フッター
├── index.html           # トップページ
├── Gemfile              # Ruby依存関係
└── README.md            # このファイル
```

## 新しいページの追加方法

1. ルートディレクトリに新しいHTMLまたはMarkdownファイルを作成
2. Front Matterを追加：

```yaml
---
layout: default
title: ページタイトル
---
```

3. コンテンツを記述

例：

```html
---
layout: default
title: プライバシーポリシー
---

<main class="min-h-screen py-8">
    <div class="max-w-7xl mx-auto px-5">
        <h1>プライバシーポリシー</h1>
        <p>内容...</p>
    </div>
</main>
```

## デプロイ

GitHub Pagesに自動デプロイされます。

```bash
git add .
git commit -m "Update site"
git push origin main
```

数分後、https://k0kishima.github.io でサイトが更新されます。

## カスタムドメインの設定

### 独自ドメイン（www.fuji.llc）の設定方法

#### 1. DNS設定

ドメインレジストラ（Squarespace Domainsなど）で以下のDNSレコードを設定：

| ホスト名 | タイプ | TTL  | データ                |
|---------|-------|------|--------------------|
| @       | A     | 3600 | 185.199.108.153    |
| @       | A     | 3600 | 185.199.109.153    |
| @       | A     | 3600 | 185.199.110.153    |
| @       | A     | 3600 | 185.199.111.153    |
| www     | CNAME | 3600 | k0kishima.github.io |

#### 2. CNAMEファイル

リポジトリのルートに `CNAME` ファイルを作成（既に作成済み）：

```
www.fuji.llc
```

#### 3. GitHub Pagesの設定

1. リポジトリの **Settings** → **Pages**
2. **Custom domain** に `www.fuji.llc` を入力
3. Save
4. DNS設定が反映されたら **Enforce HTTPS** をチェック

#### リダイレクト

GitHub Pagesが自動的に以下のリダイレクトを行います：
- `fuji.llc` → `www.fuji.llc`
- `http://www.fuji.llc` → `https://www.fuji.llc`

#### DNS反映時間

- 通常: 数分〜数時間
- 最大: 48時間

## トラブルシューティング

### Jekyllのビルドエラー（Docker）

```bash
# キャッシュをクリアして再起動
docker run --rm \
  -v "$PWD:/srv/jekyll" \
  -v jekyll-bundle:/bundle \
  -e BUNDLE_PATH=/bundle \
  jekyll/jekyll bundle exec jekyll clean

# 依存関係を再インストール
docker run --rm \
  -v "$PWD:/srv/jekyll" \
  -v jekyll-bundle:/bundle \
  -e BUNDLE_PATH=/bundle \
  jekyll/jekyll bundle install
```

それでも直らない場合は volume を作り直します。

```bash
docker volume rm jekyll-bundle
```

以下はホストに Ruby を入れている場合のみ該当します。

### Jekyllのビルドエラー（ホストの Ruby）

```bash
# 依存関係を再インストール
bundle install

# キャッシュをクリア
bundle exec jekyll clean
bundle exec jekyll serve
```

### Rubyバージョンの確認

```bash
ruby -v
rbenv version
```

### 別のRubyバージョンに切り替え

```bash
rbenv local 3.3.10
rbenv rehash
```

### Rubyバージョンが切り替わらない場合

```bash
# rbenvのshimを再構築
rbenv rehash

# パスを確認
which ruby
# /Users/your-username/.rbenv/shims/ruby と表示されるはず

# バージョン確認
ruby -v
```
