# CMScomLab Webサイト構築仕様書

## 1. 概要

本ドキュメントは、株式会社CMScomの技術ラボ「CMScomLab」の公式Webサイト構築および運用に関する仕様を定義する。
本サイトは、技術情報の発信（Blog）、プロジェクト紹介、およびラボの活動紹介を目的とし、運用負荷の低い静的サイト構成とする。

### 1.1 基本情報

* **サイト名称**: CMScomLab
* **公開URL**: `https://lab.cmscom.jp`
* **GitHub Organization**: `CMScomLab` (新規作成)
* **採用技術**: MkDocs + Material for MkDocs (Python製)

---

## 2. システム構成

サーバーレスな構成を採用し、セキュリティリスクと運用コストを最小化する。

### 2.1 アーキテクチャ

* **ソースコード管理**: GitHub (Organization: `CMScomLab`)
* **ビルド/ジェネレーター**: MkDocs (Python 3.x)
* **デザインテーマ**: Material for MkDocs
* **ホスティング**: GitHub Pages
* **デプロイ**: GitHub Actions による自動ビルド・デプロイ

### 2.2 リポジトリ構成

* **リポジトリ名**: `cmscomlab.github.io` (推奨) または `lab-site`
* **公開設定**:
* GitHub Pagesの仕様上、Freeプランの場合は `Public` 設定が必須。
* ソースコードを非公開にしたい場合（下書き等を含む場合）は、GitHub Teamプラン等を契約し `Private` リポジトリとする。



---

## 3. ディレクトリ構造

Gitリポジトリのルートディレクトリ構成は以下の通りとする。

```text
.
├── .github/
│   └── workflows/
│       └── publish.yml     # GitHub Actions設定（自動デプロイ用）
├── docs/                   # 記事・コンテンツ格納ディレクトリ
│   ├── CNAME               # カスタムドメイン設定ファイル
│   ├── index.md            # トップページ（Lab概要）
│   ├── about.md            # メンバー紹介等（必要に応じて）
│   ├── projects/           # プロジェクト紹介用フォルダ
│   │   ├── index.md        # プロジェクト一覧
│   │   ├── project-a.md    # 個別プロジェクト詳細
│   │   └── project-b.md
│   └── blog/               # ブログ/News用フォルダ
│       ├── index.md        # ブログトップ（設定により自動生成可）
│       └── posts/          # 記事ファイル置き場
│           ├── 2024-02-01-lab-open.md
│           └── 2025-xx-xx-topic.md
├── mkdocs.yml              # サイト全体設定ファイル
└── requirements.txt        # Python依存ライブラリ一覧

```

---

## 4. 環境・設定詳細

### 4.1 必須ライブラリ (requirements.txt)

ビルドに必要なPythonパッケージを定義する。

```text
mkdocs>=1.5.0
mkdocs-material>=9.5.0
mkdocs-git-revision-date-localized-plugin

```

### 4.2 サイト設定 (mkdocs.yml)

Blog機能と日本語化を有効にした基本設定。

```yaml
site_name: CMScomLab
site_url: https://lab.cmscom.jp
repo_url: https://github.com/CMScomLab/cmscomlab.github.io
edit_uri: edit/main/docs/ # 編集ボタンのリンク先

theme:
  name: material
  language: ja
  features:
    - navigation.tabs        # 上部にグローバルナビゲーションを表示
    - navigation.top         # 「トップに戻る」ボタン
    - search.suggest         # 検索サジェスト
  palette: 
    scheme: default          # default(白系) / slate(黒系)

# 拡張機能（Python開発者向けの見やすいコードブロック等）
markdown_extensions:
  - admonition
  - pymdownx.highlight:
      anchor_linenums: true
  - pymdownx.inlinehilite
  - pymdownx.snippets
  - pymdownx.superfences

# プラグイン（ブログ機能の有効化）
plugins:
  - search
  - blog:
      blog_dir: blog/posts
      post_url_format: "{date}/{slug}"
      archive: true
      categories: true

# ナビゲーション構造
nav:
  - Home: index.md
  - Projects: 
      - projects/index.md
      - 'Project A': projects/project-a.md
  - Blog: 
      - blog/index.md

```

### 4.3 自動デプロイ設定 (.github/workflows/publish.yml)

`main` ブランチにプッシュされた際、自動的にPython環境を作成し、静的ファイルを生成して `gh-pages` ブランチへデプロイする。

```yaml
name: Publish to GitHub Pages
on:
  push:
    branches:
      - main
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Configure Git Credentials
        run: |
          git config user.name github-actions[bot]
          git config user.email 41898282+github-actions[bot]@users.noreply.github.com
      - uses: actions/setup-python@v5
        with:
          python-version: 3.x
      - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV 
      - uses: actions/cache@v4
        with:
          key: mkdocs-material-${{ env.cache_id }}
          path: .cache
          restore-keys: |
            mkdocs-material-
      - run: pip install -r requirements.txt
      - run: mkdocs gh-deploy --force

```

---

## 5. ドメイン・ネットワーク設定

### 5.1 DNS設定 (DNSプロバイダ側)

`cmscom.jp` を管理するDNSサーバー（Route53, お名前.com等）にて以下のレコードを追加する。

| ホスト名 | レコードタイプ | 値 | 備考 |
| --- | --- | --- | --- |
| `lab` | CNAME | `cmscomlab.github.io` | GitHub Pagesのドメイン |

### 5.2 GitHub Pages設定

1. リポジトリ内の `docs/CNAME` ファイルに `lab.cmscom.jp` と記述してコミットする。
2. GitHubリポジトリ設定 > Pages > Custom domain に `lab.cmscom.jp` が反映されていることを確認する。
3. "Enforce HTTPS" にチェックを入れ、SSLを有効化する。

---

## 6. 運用フロー

### 6.1 記事の投稿 (Blog/News)

1. ローカル環境でブランチを切る。
2. `docs/blog/posts/` 配下にMarkdownファイルを作成する。
* ファイル名例: `2024-02-01-news-open.md`
* ヘッダー情報 (Front matter):
```yaml
---
date: 2024-02-01
categories:
  - News
authors:
  - cms_user
---
# 記事タイトル
ここに本文...

```




3. GitHubへPushし、Pull Requestを作成・マージする。
4. マージ後、数分以内にWebサイトへ自動反映される。

### 6.2 プロジェクトページの更新

1. `docs/projects/` 配下のMarkdownを編集または追加する。
2. 必要に応じて `mkdocs.yml` の `nav` セクションを更新し、メニューへのリンクを追加する。

---

**仕様書は以上です。**

この内容でプロジェクトを開始できます。まずはGitHub Organizationの作成と、上記構成ファイルの配置から始めるとスムーズです。