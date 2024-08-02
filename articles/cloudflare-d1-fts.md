---
title: "Cloudflare D1 を使った全文検索を実装する"
emoji: "🛣️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["cloudflare", "typescript", "d1", "hono"]
published: false
publication_name: "cybozu_frontend"
---

:::message
この記事は、[CYBOZU SUMMER BLOG FES '24](https://cybozu.github.io/summer-blog-fes-2024/) (Frontend Stage) DAY 5 の記事です。
:::

最近、[SQL アンチパターン](https://www.oreilly.co.jp/books/9784873115894/)という本を読んでいたら、MySQL、 PostgreSQL、SQLite などのデータベースでも拡張機能を利用することで全文検索を実装できることを知りました。[^1]

[^1]: この分野に詳しくないのですが、実務では [Elasticsearch](https://www.elastic.co/jp/elasticsearch) や [Meilisearch](https://www.meilisearch.com/) などの専用のライブラリの利用を検討した方が良いと思います。

SQLite で構築されている Cloudflare D1 についても調べてみたところ、[制限はあるものの全文検索の拡張機能が使える](https://developers.cloudflare.com/d1/build-with-d1/import-export-data/#known-limitations-1)ということがわかりました。

> Export is not supported for virtual tables, including databases with virtual tables. D1 supports virtual tables for full-text search using SQLite’s FTS5 module.

今回は個人ブログの検索機能を D1 を利用して実装してみたので、その方法について書きたいと思います。

## 作成した API

全文検索用のエンドポイント (`/api/fts/search`) とデータ登録用のエンドポイント (`/api/fts/insert`) を Cloudflare Workers と D1 を使って実装しました。各 API の仕様は次の通りです。

### POST /api/fts/insert

- 認証：あり (今回は Basic 認証を実装)
- リクエストボディ

```json:リクエストボディのサンプル
{
  "id": "post-1",
  "title": "タイトル",
  "content": "ブログのコンテンツ"
}
```

- レスポンス：
  - `201`：データ登録に成功した場合

### GET /api/fts/search

- 認証：なし
- クエリ
  - `q`：検索クエリ
- レスポンス：
  - `200`：検索に成功した場合
  - `400`：検索クエリがなかった場合

```json: 検索のレスポンスのサンプル
{
  "posts": [
    {
      "id": "post-1",
      "title": "タイトル1",
      "content": "ブログのコンテンツ1"
    },
    {
      "id": "post-2",
      "title": "タイトル2",
      "content": "ブログのコンテンツ2"
    }
  ]
}
```

## プロジェクトの作成

今回は [Hono](https://hono.dev/) を利用して、Cloudflare Workers を利用した API の実装を行います。次のコマンドでプロジェクトを作成します。

```shell
pnpm create hono@latest blog-fts
```

また、D1 対応している ORM として [drizzle-orm](https://orm.drizzle.team/) も使いたかったのですが、全文検索の実装に必要な [Virtual Table のサポートがまだされていない](https://github.com/drizzle-team/drizzle-orm/issues/2046)ようなので、今回は利用しませんでした。[^2]

[^2]: [PostgreSQL での全文検索はサポートされているようです。](https://orm.drizzle.team/learn/guides/postgresql-full-text-search)

## テーブル作成

SQLite では全文検索のための fts テーブルを次のように定義します。col1、col2 となっている部分には、実際の検索対象となるドキュメントを格納します。

```sql
CREATE VIRTUAL TABLE table_name USING fts5(col1, col2, ...);
```

ここで重要になるのが、ドキュメントを検索対象となる単位のトークンに分割する tokenizer のオプションです。SQLite の拡張で提供されている選択肢は、次の 4 つになっています。

- unicode61 tokenizer (デフォルト)
  - Unicode 6.1.0 で定義されているスペースや句読点 (punctuation) で分割する方法
- ascii tokenizer
  - unicode61 tokenizer を拡張したもの
- porter tokenizer
  - unicode61 tokenizer などと組み合わせて利用し、出力された token を porter stemmer という手法で正規化する方法
  - ex) running, ran, run → run
- trigram tokenizer
  - 3 文字をトークンの単位とし、文字列の先頭から 1 文字ずつ順番に分割する方法

これらの tokenizer は、どれも日本語の場合を考慮できておらず、検索の精度に大きな影響を及ぼします。**そこで今回の実装では、格納するドキュメントを事前にトークンに分割し (分かち書きし)、得られたトークンをスペース区切りでつなげたものを fts のテーブルへ格納するようにしました。**[^3] また、ドキュメント自体は別のテーブルで管理するようにしました。実際に作成したテーブル定義は、次のようになります。

[^3]: Kagome と呼ばれる日本語の形態素解析を行うツールの[サンプルコード](https://github.com/ikawaha/kagome/tree/v2/_examples/db_search)を参考にしました。

```sql:schema.sql
CREATE TABLE IF NOT EXISTS contents (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  post_id TEXT NOT NULL UNIQUE,
  title TEXT NOT NULL,
  content TEXT NOT NULL
);
// content と title に格納するドキュメントを分かち書きし、
// トークンをスペース区切りでつなげたものを segments へ格納する
CREATE VIRTUAL TABLE IF NOT EXISTS fts USING fts5(segments);
```

上記のテーブルを D1 へ作成するには次のコマンドを実行します。

```sql
// 新規のデータベースの作成
wrangler d1 create blog-fts
// SQL の実行
wrangler d1 execute blog-fts --file=./schema.sql
```

## API の実装

DB の準備ができたので、次に API の実装をしていきます。

### POST /api/fts/insert

分かち書きには

### GET /api/fts/search

## 終わりに

実装の全体を参考にしたい方は、次のコードを参考にしてもらえればと思います。

https://github.com/nissy-dev/blog/tree/7c18433e286e96dfede54e24f6ee00d033ffa02b/packages/fts
