# Qiita 記事管理リポジトリ

[Qiita CLI](https://github.com/increments/qiita-cli) を使って記事を管理するリポジトリ。
main ブランチへの push で GitHub Actions が自動的に Qiita へ投稿・更新する。

## セットアップ

```bash
pnpm install
npx qiita login
```

`qiita login` でQiitaのアクセストークンを登録する。（ローカルでのプレビューや pull に必要）
トークンは https://qiita.com/settings/tokens/new から `read_qiita` と `write_qiita` の権限を付けて発行する。

GitHub Actions 用には、リポジトリの Settings > Secrets に `QIITA_TOKEN` を登録する。

## 記事の作成

```bash
npx qiita new 記事のファイル名
```

`public/` 配下にMarkdownファイルが生成される。

### Front Matter

ファイル先頭のメタデータ。`npx qiita new` で自動生成される。

```yaml
---
title: 記事タイトル
tags:
  - タグ1
  - タグ2
private: false       # true: 限定共有記事 / false: 公開記事
updated_at: ''       # 投稿後に自動で更新される
id: null             # 投稿後に自動で付与される
organization_url_name: null
slide: false
ignorePublish: false # true にすると publish --all の対象外になる
---
```

## プレビュー

```bash
npx qiita preview
```

http://localhost:8888 でプレビューが開く。

## 記事の投稿・更新

main ブランチに push すると、GitHub Actions が自動で全記事を Qiita に投稿・更新する。
front matter の `id` が `null` なら新規投稿、値があれば更新になる。

投稿後、CI が `id` と `updated_at` を更新してコミットするので、ローカルで `git pull` して取り込む。

## 投稿後のファイルリネーム

初回投稿後、`git pull` で `id` が付与されたら、ファイル名を `YYYYMMDD_<id>.md` の形式にリネームする。

- `YYYYMMDD` : 投稿日
- `<id>` : front matter の `id`（Qiita の記事ID）

```bash
# 1. CI が更新した id を取り込む
git pull

# 2. リネーム
git mv public/newArticle001.md public/20260210_97b122223bbd5d3b9e6f.md
```

## Qiitaから記事を取得

```bash
npx qiita pull
```

Qiita上の記事をローカルに同期する。初回セットアップ時や他の環境で編集した記事を取り込むときに使う。

## 注意事項

- 記事の削除は Qiita CLI からはできない。Qiitaのサイト上で行う。
