# CMScomLab Website

## 技術スタック

- MkDocs
- Material for MkDocs（Blogプラグイン含む）
- mkdocs-git-revision-date-localized-plugin
- Python（`uv` で実行）
- GitHub Actions + GitHub Pages（`gh-pages` ブランチにデプロイ）

## ディレクトリ構成（主要部分）

- `docs/` : すべてのコンテンツ
- `docs/blog/index.md` : ブログ一覧ページ
- `docs/blog/posts/` : ブログ本文（Markdown）
- `docs/blog/.authors.yml` : 著者定義
- `docs/assets/images/` : 画像
- `mkdocs.yml` : MkDocs 設定
- `.github/workflows/publish.yml` : デプロイ用ワークフロー

## ローカル開発 / ビルド

開発サーバ:

```
uv run mkdocs serve
```

静的サイトビルド:

```
uv run mkdocs build
```

## デプロイ（GitHub Pages）

`main` に push すると GitHub Actions が起動し、`mkdocs gh-deploy --force` により
`gh-pages` ブランチへ静的サイトが配置されます。

GitHub Pages の設定は以下:
- Source: Deploy from a branch
- Branch: `gh-pages` / `(root)`

カスタムドメインを使う場合は `docs/CNAME` を更新し、GitHub Pages 側でも同じドメインを設定します。

## Blogの著者を追加する方法

Blogの著者は `docs/blog/.authors.yml` で管理します。  
1人追加するたびにここへ定義を足してください。

### 1) 著者情報を追加

`docs/blog/.authors.yml` に追記します。

```yaml
authors:
  terapyon:
    name: Manabu TERADA (terapyon)
    description: Founder of CMScomLab.
    avatar: assets/images/avatar-terapyon.jpg
```

必須項目:
- `name`
- `description`
- `avatar`（`docs/` からの相対パス）

任意項目:
- `slug`
- `url`

### 2) 画像を配置

`avatar` で指定した場所に画像を置きます。  
例: `docs/assets/images/avatar-<id>.jpg`

### 3) 記事のフロントマターで著者を指定

投稿ファイル（`docs/blog/posts/*.md`）のフロントマターで著者IDを指定します。

```yaml
---
date: 2026-02-01
categories:
  - News
authors:
  - terapyon
---
```

### 4) 確認

ローカルで確認:

```
uv run mkdocs serve
```
