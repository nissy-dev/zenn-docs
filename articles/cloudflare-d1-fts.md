---
title: "Cloudflare D1 を使った日本語の全文検索を実装する"
emoji: "🔍"
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

SQLite で構築されている [Cloudflare D1](https://developers.cloudflare.com/d1/) についても調べてみたところ、[制限はあるものの全文検索の拡張機能が使える](https://developers.cloudflare.com/d1/build-with-d1/import-export-data/#known-limitations-1)ということがわかりました。

> Export is not supported for virtual tables, including databases with virtual tables. D1 supports virtual tables for full-text search using SQLite’s FTS5 module.

今回は個人ブログの全文検索 API を D1 を利用して実装してみたので、その方法について書きたいと思います。

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

今回は [Hono](https://hono.dev/) を利用して API の実装を行いました。次のコマンドでプロジェクトの雛形を作成します。

```shell
pnpm create hono@latest blog-fts
```

また、D1 対応している ORM として [drizzle-orm](https://orm.drizzle.team/) も利用したかったのですが、全文検索の実装に必要な [Virtual Table のサポートがまだされていない](https://github.com/drizzle-team/drizzle-orm/issues/2046)ようなので、今回は見送りました。[^2]

[^2]: [PostgreSQL での全文検索はサポートされているようです。](https://orm.drizzle.team/learn/guides/postgresql-full-text-search)

## テーブルの作成

SQLite では全文検索のための fts テーブルを次のように定義します。カラムには、実際の検索対象となるドキュメントを格納します。

```sql
CREATE VIRTUAL TABLE table_name USING fts5(column1, column2, tokenizer='trigram', ... );
```

ここで重要になるのが、ドキュメントを検索対象の単位のトークンに分割する tokenizer のオプションです。SQLite の拡張で提供されている選択肢は、次の 4 つになっています。

- unicode61 tokenizer (デフォルト)
  - Unicode 6.1.0 で定義されているスペースや句読点 (punctuation) で分割する方法
- ascii tokenizer
  - unicode61 tokenizer をベースに、ascii 文字に関してルールを追加して分割する方法
- porter tokenizer
  - 他の tokenizer と組み合わせて利用し、出力されたトークンを porter stemmer というアルゴリズムで正規化する方法
  - 例) running, ran などの活用形が変化しているトークンを run に統一する
- trigram tokenizer
  - 3 文字をトークンの単位とし、文字列の先頭から 1 文字ずつ順番に分割する方法

これらの tokenizer は、どれも日本語の場合を考慮できておらず、検索の精度があまり良くありません。**今回の実装では、格納するドキュメントを別のツールを使ってトークンに分割し、得られたトークンをスペース区切りでつなげたものを fts テーブルへ格納するようにしました。**[^3] また、ドキュメント自体は別のテーブルで管理するようにしました。実際に作成したテーブル定義は、次のようになります。

[^3]: Kagome と呼ばれる日本語の形態素解析を行うツールの[サンプルコード](https://github.com/ikawaha/kagome/tree/v2/_examples/db_search)を参考にしました。

```sql:schema.sql
CREATE TABLE IF NOT EXISTS contents (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  post_id TEXT NOT NULL UNIQUE,
  title TEXT NOT NULL,
  content TEXT NOT NULL
);
// content と title に格納するドキュメントを別のツールを使って分かち書きし、
// トークンをスペース区切りでつなげたものを segments へ格納する
CREATE VIRTUAL TABLE IF NOT EXISTS fts USING fts5(segments);
```

作成するテーブル定義を決めたので、次のコマンドで D1 へ反映します。

```shell
# 新規のデータベースの作成
wrangler d1 create blog-fts
# SQL の実行 (本番環境に向けるときは --local を --remote にする)
wrangler d1 execute blog-fts --local --file=./schema.sql
```

## API の実装

DB の準備ができたら、Hono を利用した API の実装をしていきます。

```ts:src/index.ts
import { Hono } from "hono";

type Bindings = {
  DB: D1Database;
  AUTH_USERNAME: string;
  AUTH_PASSWORD: string;
};

const app = new Hono<{ Bindings: Bindings }>();

// POST /api/fts/insert
app.post("/api/fts/insert", ...);

// GET /api/fts/search
app.get("/api/fts/search", ...)

export default app;
```

### POST /api/fts/insert

データ登録用のエンドポイントでは、主に次の処理を行います。

1. 受け取ったデータを contents テーブルに追加する
2. [Intl.Segmenter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter) を利用して、受け取ったデータからトークンを抽出する
3. 作成したトークンをスペース区切りで結合し、fts テーブルに追加する

ハンドラー部分の具体的なコードは次のようになりました。

```ts:src/index.ts
import { basicAuth } from "hono/basic-auth";

type Post = {
  id: string;
  title: string;
  content: string;
};

const segmenter = new Intl.Segmenter("ja", { granularity: "word" });

app.post(
  "/api/fts/insert",
  // Basic認証用の設定
  async (c, next) => {
    const auth = basicAuth({
      username: c.env.AUTH_USERNAME,
      password: c.env.AUTH_PASSWORD,
    });
    await auth(c, next);
  },
  async (c) => {
    const post = await c.req.json<Post>();
    const { id, title, content } = post;
    const db = c.env.DB;

    // 1. 受け取ったデータを contents テーブルに追加する
    const res = await db
      .prepare(
        "INSERT INTO contents (post_id, title, content) VALUES (?1, ?2, ?3)",
      )
      .bind(id, title, content)
      .run();
    // レスポンスから追加時の rowid を取得する (fts テーブルへのデータ追加時に利用する)
    const rowId = res.meta.last_row_id;

    // 2. Intl.Segmenter を利用して、受け取ったデータからトークンを抽出する
    const segments = Array.from(segmenter.segment(`${title}。${content}`))
      .filter((s) => s.isWordLike)
      .map((s) => s.segment);

     // 3. 作成したトークンをスペース区切りで結合し、fts テーブルに追加する
    await db
      .prepare("INSERT INTO fts (rowid, segments) VALUES (?1, ?2)")
      .bind(rowId, segments.join(" "))
      .run();

    c.status(201);
    return c.body(null);
  },
);
```

文書のトークンへの分割 (分かち書き) には、[Intl.Segmenter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter) を利用しました。IPA 辞書を利用した単語分割を行なっているようで、詳しいアルゴリズムについては次の記事を参考にしてもらえればと思います。

https://zenn.dev/cybozu_frontend/articles/explore-intl-segmenter

検索精度のことを考えると、本当は [sudachi](https://github.com/WorksApplications/sudachi.rs) や [lindera](https://github.com/lindera-morphology/lindera) などの日本語に特化した形態素解析のツールを利用した方が良いと思うのですが、JS からこれらのツールを簡単に利用できなさそうだったので今回は見送りました。[^4]

[^4]: [lindera-js](https://github.com/higumachan/lindera-js) や [sudachi-wasm](https://github.com/hata6502/sudachi-wasm) などの wasm ベースのツールはあったのですが、更新があまりされていないようなので利用しませんでした。

### GET /api/fts/search

全文検索用のエンドポイントでは、主に次の処理を行います。

1. 検索クエリが渡されているかのバリデーション
2. 検索クエリにマッチしたドキュメントを fts と contents テーブルを利用して取得する

ハンドラー部分の具体的なコードは次のようになりました。

```ts:src/index.ts
app.get("/api/fts/search", async (c) => {
  // 1. 検索クエリが渡されているかのバリデーション
  const query = c.req.query("q");
  if (!query) {
    return c.json({ error: "Query parameter 'q' is required" }, 400);
  }

  const db = c.env.DB;
  // 2. 検索クエリにマッチしたドキュメントを fts と contents テーブルを利用して取得する
  // ランクが高い順に最大 5件のデータを返す用意している
  const { results } = await db
    .prepare(
      `SELECT contents.post_id as id, contents.title, contents.content FROM contents
       JOIN fts ON contents.rowid = fts.rowid
       WHERE fts MATCH ?1
       ORDER BY rank
       LIMIT 5`,
    )
    .bind(query)
    .all<Post>();

  return c.json({ posts: results });
});
```

## API の挙動の確認

次のコマンドを実行することで、ローカルで挙動を確認できます。

```shell
# ローカルでの API サーバーの起動 (本番環境へのデプロイは wrangler deploy コマンドを使う)
wrangler dev src/index.ts

# データの登録
curl "http://localhost:8787/api/fts/insert" \
  -X POST \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <token>" \
  -d '{"id" : "post-1" , "title" : "タイトル1", "content" : "ブログのコンテンツ1"}'

# データの検索
curl "http://localhost:8787/api/fts/search?q=keyword"
```

## まとめ

今回の記事では、個人ブログの検索のための API を Cloudflare Workers と D1 を利用して実装する方法を紹介しました。実装の全体を確認したい方は、次のコードを参考にしてもらえればと思います。

https://github.com/nissy-dev/blog/tree/7c18433e286e96dfede54e24f6ee00d033ffa02b/packages/fts

今回は全文検索を実装しましたが、Cloudflare は[ベクトルデータベースも提供している](https://developers.cloudflare.com/vectorize/)ので、検索機能を実装する選択肢としてベクトル検索もありそうです。

https://zenn.dev/laiso/articles/7a21b5bf14f10c

ただ、上記の記事でも触れられているのですが、[Cloudflare からは英語を対象としたベクトル生成用の LLM しか提供されていません](https://developers.cloudflare.com/workers-ai/models/#text-embeddings)。その部分については OpenAI などの API を使うしかないようです。Cloudflare は、今回利用した D1 以外にも個人で色々試すには良い機能をたくさん提供してくれているので、日本語に対応した LLM も使えるようになることを期待しています。
