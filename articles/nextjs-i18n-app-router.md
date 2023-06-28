---
title: "Pages Router と App Router での i18n 対応の違い"
emoji: "🗺️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "nextjs", "i18n"]
published: false
publication_name: "cybozu_frontend"
---

Next.js で作っていた個人ブログの App Router への移行を試みていたのですが、軽い気持ちで実装していた i18n 周りの移行に苦労しました。このブログでは、ライブラリを使わずに i18n 対応する際の Pages Router と App Router での実装方法の違いについて紹介したいと思います。

:::message
自分の個人ブログでは、[Static Exports](https://nextjs.org/docs/pages/building-your-application/deploying/static-exports) を利用していません。Static Exports を利用している場合には、[こちらの記事](https://blog.arthur1.dev/entry/2023/06/04/100000)が参考になるかもしれません。
:::

## i18n 対応で必要なこと

個人ブログの i18n 対応でやっていることは、大きく次の２つになります。

- ルーティングと言語の判定
  - `/blog` の場合には、デフォルトの locale ("ja") を割り当てる
  - `/en/blog` の場合には、パスで指定されている locale ("en") を割り当てる
- 文言のローカライゼーション
  - locale に応じて `t("some-key")` のような関数で文言を出し分ける

それぞれについて、Pages Router と App Router の場合の実装を見ていきます。

## Pages Router での i18n 対応

まず、「ルーティングと言語の判定」についてですが、こちらについては Next.js が提供している i18n Routing という機能を使って対応することができます。

https://nextjs.org/docs/pages/building-your-application/routing/internationalization

今回の場合では、実際に next.config.js に次のような設定を追加するだけで対応が完了します。ドメインに応じた defaultLocale の割り当てにも対応しており、非常に便利な機能だと思います。

```js:next.config.js
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

```ts:i18n/useTranslation.tsx
import { useCallback } from "react";
import { useRouter } from "next/router";

import {
  RESOURCES,
  DEFAULT_LOCALE,
  isSupportLocale,
  i18Key,
  Locale
} from "./resources";

export const useTranslation = (): {
  t: (key: i18nKey) => string;
  locale: Locale;
} => {
  const { locale } = useRouter();
  const currentLocale = isSupportLocale(locale) ? locale : DEFAULT_LOCALE;

  const translate = useCallback(
    (key: i18nKey) => {
      return RESOURCES[currentLocale][key];
    },
    [currentLocale]
  );
  return { t: translate };
};
```

```ts:i18n/resource.ts
import en from "./locales/en.json";
import ja from "./locales/ja.json";

export const RESOURCES = { ja, en };
export const SUPPORTED_LOCALES = Object.keys(RESOURCES) as Locale[];
export const DEFAULT_LOCALE = "ja";

export type Locale = keyof typeof RESOURCES;
export type i18nKey = keyof (typeof RESOURCES)["ja"];

export const isSupportLocale = (locale: string | undefined): locale is Locale =>
  locale !== undefined && Object.keys(RESOURCES).includes(locale);
```

Pages Router の場合は、このように外部のライブラリに頼る必要もなく簡単に実装できるということもあり、個人ブログでもサイト内の一部のコンテンツに限定して i18n の対応をしていました。

## App Router での i18n 対応

App Router では Page Router でサポートされていた機能の多くを利用することができず、自分で実装する必要がありました。「ルーティングと言語の判定」と「文言のローカライゼーション」について、それぞれ詳しく説明していきます。

### ルーティングと言語の判定

Pages Router の場合に利用した i18n Routing ですが、App Router では利用できなくなりました。このため、同様の処理を [middleware](https://nextjs.org/docs/app/building-your-application/routing/middleware) や [rewrites](https://nextjs.org/docs/app/api-reference/next-config-js/rewrites) の機能を使って対応する必要があります。

https://nextjs.org/docs/app/building-your-application/routing/internationalization

今回の個人ブログの場合では、`app/[locale]` ディレクトリを作成し、次のような処理を実装することで対応しました。

- middleware を利用して、ユーザーの locale が デフォルト値 ("ja") でない場合に locale の prefix をパスに付与してリダイレクトさせる
- rewrites を利用して、パスに locale の prefix がない場合にデフォルトの locale ("ja") を割り当てる

まず middleware の実装は次のようになります。公式ドキュメントの実装を参考にしつつ、リダイレクトの条件だけを変えています。

```ts:middleware.ts
import Negotiator from "negotiator";
import { NextRequest, NextResponse } from "next/server";

import { SUPPORTED_LOCALES, DEFAULT_LOCALE } from "./i18n/resources";

const extractLocale = (headers: Negotiator.Headers) => {
  return (
    new Negotiator({ headers }).language(SUPPORTED_LOCALES) ?? DEFAULT_LOCALE
  );
};

export function middleware(request: NextRequest) {
  const pathname = request.nextUrl.pathname;
  const pathnameIsMissingLocale = SUPPORTED_LOCALES.every(
    (locale) => !pathname.startsWith(`/${locale}/`) && pathname !== `/${locale}`
  );
  // Accept-Language ヘッダーから locale を取得する
  const headers = {
    "accept-language": request.headers.get("accept-language") ?? "",
  };
  const locale = extractLocale(headers);

  // ユーザの locale が デフォルト値と同じ場合は、locale の prefix をつけてリダイレクトさせない
  if (pathnameIsMissingLocale && locale !== DEFAULT_LOCALE) {
    return NextResponse.redirect(
      new URL(`/${locale}/${pathname}`, request.url)
    );
  }
}

export const config = {
  matcher: ["/((?!_next).*)"],
};
```

ユーザの locale がデフォルト値と同じになる場合には、リダイレクトさせないようにしました。これは、"ja" ロケールのユーザーが `/ja/blog` にリダイレクトしないようにするためです。

そして、パスに locale を含まないかつユーザーの locale がデフォルト値の場合については、次のような rewrites の設定を書くことによってデフォルトの locale を割り当てました。

```js:next.config.js
module.exports = {
  async rewrites() {
    return [
      {
        source: "/blog",
        destination: "/ja/blog",
      },
      ....
    ];
  },
};
```

### 文言のローカライゼーション

Pages Router の際に利用していた `useRouter` の帰り値に含まれる locale ですが、こちらについても App Router では利用できなくなりました。

https://nextjs.org/docs/pages/building-your-application/upgrading/app-router-migration#step-5-migrating-routing-hooks

そのため、代わりに `usePathname` の返り値から locale を取得して、Pages Router のときと同様の hooks を実装しました。

```ts:i18n/useTranslation.tsx
import { useRouter } from "next/router";
import { useCallback } from "react";

import {
  RESOURCES,
  SUPPORTED_LOCALES,
  DEFAULT_LOCALE,
  isSupportLocale,
  i18nKey,
  Locale
} from "./resources";

export const useTranslation = (): {
  t: (key: i18nKey) => string;
  locale: Locale;
} => {
  const pathname = usePathname();
  // pathname に locale の prefix が含まれている場合に取得する
  const pathnameWithLocale = SUPPORTED_LOCALES.some((locale) =>
    pathname.startsWith(`/${locale}`)
  );
  const currentLocale = pathnameWithLocale
    ? pathname.split("/")[1]
    : DEFAULT_LOCALE;

  if (!isSupportLocale(currentLocale)) {
    throw new Error(`Unsupported locale: ${currentLocale}`);
  }

  const translate = useCallback(
    (key: i18nKey) => {
      return RESOURCES[currentLocale][key];
    },
    [currentLocale]
  );
  return { t: translate };
};
```

また、App Router で利用できる Server Component では hooks を利用できません。このため、Server Components で使う文言取得用の関数も別で定義します。

```ts:i18n/getTransition.tsx
import "server-only";

import { RESOURCES, SUPPORTED_LOCALES, Locale, i18nKey } from "./resources";

export const getTranslation = (locale: Locale) => {
  if (!SUPPORTED_LOCALES.includes(locale)) {
    throw new Error(`Unsupported locale: ${locale}`);
  }
  return { t: (key: i18nKey) => RESOURCES[locale][key] };
};
```

## 移行した感想

上記の手順で App Router でもライブラリを導入することなく i18n の機能を移行できました。一方で、やはり Page Router の場合の方が i18n を簡単に導入できたので、移行した感想としては次の２点が残る感じにはなりました。

- middleware の実装はなるべく楽をしたい
- Server Components と Client Components で文言取得用の関数を使い分けたくない

これらの課題感を解消するようなライブラリがあるのか調べたところ、[next-translate](https://github.com/aralroca/next-translate) が良さそうに感じました。

https://aralroca.com/blog/i18n-translations-nextjs-13-app-dir

特に、文言取得用の関数を Server Components と Client Components 向けに同じ useTranslation という API で提供しているのには驚きました。個人的にどのように実現しているのか気になって調べたところ、ビルド時に動的にコードを書き換えているようです。

https://github.com/aralroca/next-translate-plugin/blob/67e728a9d2881de3d4ccc15f40ecf9f89899415a/src/templateAppDir.ts

この実装方法では Webpack から Turbopack への移行の障害にもなってしまいそうなので気になりますが、今度どこかで使う機会があれば試してみたいと思います。
