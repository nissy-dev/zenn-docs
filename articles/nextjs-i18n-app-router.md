---
title: "個人ブログの App Router 移行における i18n 周りの対応について"
emoji: "🗺️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "next.js", "i18n"]
published: false
publication_name: "cybozu_frontend"
---

Next.js で作っていた個人ブログの Pages Router から App Router へ移行を試みていたのですが、軽い気持ちで実装していた i18n 周りの移行に苦労しました。このブログでは、具体的な移行手順を紹介しながら苦労した点などを紹介したいと思います。

:::message
自分の個人ブログでは、[Static Exports](https://nextjs.org/docs/pages/building-your-application/deploying/static-exports) を利用していません。Static Exports を利用している場合には、[こちらの記事](https://blog.arthur1.dev/entry/2023/06/04/100000)が参考になるかもしれません。
:::

## ３行まとめ

## i18n 対応で必要なこと

個人ブログの i18n 対応でやっていることは、大きく次の２つになります。

- ルーティングと言語の判定
  - `/blog` の場合には、デフォルトの locale ("ja") を割り当てる
  - `/en/blog` の場合には、パスで指定されている locale ("en") を割り当てる
- 文言のローカライゼーション
  - locale に応じて `t("some-key")` のような関数で文言を出し分ける

それぞれについて、Pages Router と App Router の時の実装を見ていきます。

## Pages Router での i18n 対応

まず、「ルーティングと言語の判定」についてですが、こちらについては Next.js が提供している i18n Routing という機能を使って対応することができます。

https://nextjs.org/docs/pages/building-your-application/routing/internationalization

今回の場合では、実際に next.config.js に次のような設定を追加するだけで対応が完了します。ドメインに応じたデフォルト locale の振り分けにも対応しており、非常に便利な機能だと思います。

```js
module.exports = {
  i18n: {
    // 対応したい全ての locale
    locales: ['en-US', 'ja'],
    // URL パスに locale のプレフィックスがない場合に利用される locale
    defaultLocale: 'ja',
  },
  ...
}
```

次に、「文言のローカライゼーション」についてですが、locale の情報が `useRouter` から取得できるので、その locale を利用して文言取得用の関数 `t("some-key")` を返す hooks を書いて対応しました。

```js
import { useRouter } from "next/router";
import { useCallback } from "react";

import en from "../../public/locales/en.json";
import ja from "../../public/locales/ja.json";

const resources = { ja, en };

const isSupportLocale = (locale) => Object.keys(resources).includes(locale);

export const useTranslation = () => {
  const { locale } = useRouter();
  const currentLocale = isSupportLocale(locale) ? locale : "ja";

  const translate = useCallback(
    (key) => {
      return resources[currentLocale][key];
    },
    [currentLocale]
  );
  return { t: translate };
};
```

Pages Router の場合では、このように外部のライブラリに頼る必要もなく簡単に対応できるということもあり、個人ブログでもサイト内の一部のコンテンツに限定して対応してみていました。

## App Router での i18n 対応

### ルーティングと言語の判定

まず、「ルーティングと言語の判定」についてですが、App Router では i18n Routing という機能が使えなくなりました。このため、同様の処理を [middleware](https://nextjs.org/docs/app/building-your-application/routing/middleware) や [rewrites](https://nextjs.org/docs/app/api-reference/next-config-js/rewrites) の機能を使って対応する必要があります。

https://nextjs.org/docs/app/building-your-application/routing/internationalization

今回の個人ブログの場合では、次のような流れで対応しました。

- locale の情報をコンポーネントで受け取るために `app/[locale]` というディレクトリを作成する
- `Accept-Language` ヘッダーのデータから locale が デフォルト値 ("ja") 以外だった場合に URL へ locale の prefix をつけてリダイレクトさせる middleware を実装する
- URL に locale の prefix がない場合は、rewrites の機能を使ってデフォルトの locale ("ja") を割り当てる

middleware は次のように実装しました。

### 文言のローカライゼーション

## ライブラリはあるのか？
