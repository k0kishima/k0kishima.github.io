# Fuji Enterprise コーポレートサイト

富士エンタープライズの公式コーポレートサイトです。

## 必要な環境

- Ruby 3.3.x 以上
- rbenv
- Bundler
- Jekyll

## セットアップ手順

### 1. rbenvのインストール（未インストールの場合）

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

### 2. Ruby 3.3系の最新版をインストール

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

### 3. Bundlerのインストール

```bash
gem install bundler
```

### 4. Gemfileの作成とJekyllのインストール

このリポジトリには既にGemfileがあるので、以下のコマンドを実行：

```bash
bundle install
```

### 5. ローカルサーバーの起動

```bash
bundle exec jekyll serve
```

ブラウザで http://localhost:4000 にアクセスすると、サイトをプレビューできます。

ファイルを編集すると自動的にリロードされます（一部のファイルは再起動が必要）。

### 6. サーバーの停止

`Ctrl + C` でサーバーを停止します。

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

## トラブルシューティング

### Jekyllのビルドエラー

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
