---
title: "Pages Router と App Router での i18n 対応の違い"
emoji: "🌐"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "nextjs", "i18n"]
published: true
publication_name: "cybozu_frontend"
---

Next.js で作っていた個人ブログの App Router への移行を試みていたのですが、軽い気持ちで実装していた i18n 周りの移行に苦労しました。このブログでは、ライブラリを使わずに i18n 対応する際の Pages Router と App Router での実装方法の違いについて紹介したいと思います。

:::message
自分の個人ブログでは、[Static Exports](https://nextjs.org/docs/pages/building-your-application/deploying/static-exports) を利用していません。Static Exports を利用している場合には、[こちらの記事](https://blog.arthur1.dev/entry/2023/06/04/100000)が参考になるかもしれません。
:::

## ３行まとめ

- Pages Router で利用できた [i18n Routing](https://nextjs.org/docs/pages/building-your-application/routing/internationalization) が App Router では利用できない
- App Router で i18n 対応を実装するには、Next.js の [middleware](https://nextjs.org/docs/app/building-your-application/routing/middleware) や [rewrites](https://nextjs.org/docs/app/api-reference/next-config-js/rewrites) などの機能をうまく活用する
- App Router 向けの i18n 対応向けのライブラリは、ビルド時にコードを大きく書き換えており、Tubopack などを見据えた将来的な安定性に少し不安がある

## i18n 対応で必要なこと

個人ブログの i18n 対応でやっていることは、大きく次の２つになります。

- ユーザーの locale の判定とルーティング
  - デフォルトの locale ("ja") の場合には `/blog` へ向ける
  - デフォルトの locale 以外 ("en") の場合には `/en/blog` へ向ける
- 文言のローカライゼーション
  - locale に応じて `t("some-key")` のような関数で文言を出し分ける

それぞれについて、Pages Router と App Router の場合の実装を見ていきます。

## Pages Router での i18n 対応

まず、「ユーザーの locale の判定とルーティング」についてですが、こちらについては Next.js が提供している i18n Routing という機能を使って対応することができます。

https://nextjs.org/docs/pages/building-your-application/routing/internationalization

今回の場合では、実際に next.config.js に次のような設定を追加するだけで対応が完了します。ドメインに応じた defaultLocale の割り当てにも対応しており、非常に便利な機能だと思います。

```js:next.config.js
module.exports = {
  i18n: {
    // 対応したい全ての locale
    locales: ['en', 'ja'],
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

import { RESOURCES, DEFAULT_LOCALE, isSupportLocale, i18Key, Locale } from "./resources";

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

:::details resources.ts の実装について

次のように汎用的に利用するデータや型を定義して export しています。

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

:::

今回定義した hooks については、例えば `/blog` のエントリーになるファイルで次のように利用します。

```tsx:pages/blog.tsx
import { SUPPORTED_LOCALES } from "../i18n/resources";
import { useTranslation } from "../i18n/useTranslation";

export const getStaticPaths = ({ locales }) => {
  return {
    paths: SUPPORTED_LOCALES.map((locale) => ({ locale })),
    fallback: true,
  }
}

export default function BlogHome() {
  const { t } = useTranslation()
  return <div>{t('blog-home')}</div>
}
```

Pages Router の場合は、このように外部のライブラリに頼る必要もなく簡単に実装できるということもあり、個人ブログでもサイト内の一部のコンテンツに限定して i18n の対応をしていました。

## App Router での i18n 対応

App Router では Page Router でサポートされていた機能の多くを利用することができず、自分で実装する必要がありました。「ユーザーの locale の判定とルーティング」と「文言のローカライゼーション」について、それぞれ詳しく説明していきます。

### ユーザーの locale の判定とルーティング

Pages Router の場合に利用した i18n Routing ですが、App Router では利用できなくなりました。このため、同様の処理を [middleware](https://nextjs.org/docs/app/building-your-application/routing/middleware) や [rewrites](https://nextjs.org/docs/app/api-reference/next-config-js/rewrites) の機能を使って対応する必要があります。

https://nextjs.org/docs/app/building-your-application/routing/internationalization

今回の場合では、`app/[locale]/blog` のようなディレクトリを作成し、URL に locale の prefix がついていない場合に関して次のような処理を実装することで対応しました。

- ユーザーの locale がデフォルト値 ("ja") でない場合に、middleware を利用して locale の prefix をパスに付与してリダイレクトさせる
- ユーザーの locale がデフォルト値 ("ja") である場合に、rewrites を利用してデフォルトの locale ("ja") を含むパスにマッピングする

![](/images/nextjs-i18n-app-router/app-router-abstract.png)
_リクエストが処理される流れ。_

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

ユーザの locale がデフォルト値と同じになる場合には、リダイレクトさせないようにしました。これは、デフォルトロケールである "ja" ロケールのユーザーが `/ja/blog` にリダイレクトしないようにするためです。

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

そのため、params として取得できる locale を Context としてアクセスできるようにし、Pages Router のときと同様の hooks を実装しました。

```ts:i18n/client.ts
import { createContext, useContext, useCallback } from "react";

import { RESOURCES, DEFAULT_LOCALE, Locale, i18nKey, isSupportLocale } from "./resources";

export const LocaleContext = createContext<Locale>(DEFAULT_LOCALE);

export const useTranslation = (): {
  t: (key: i18nKey) => string;
} => {
  const currentLocale = useContext(LocaleContext);
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

利用方法としては、まず `LocaleContext` の Provider を `layout.tsx` で追加します。

```tsx:app/[locale]/blog/layout.tsx
import React from "react";
import { LocaleContext } from "../../../i18n/client";

export default function Layout({
  children,
  params,
}: {
  children: React.ReactNode;
  params: { locale: string };
}) {
  return (
    <html lang={params.locale}>
      <body>
        <LocaleContext.Provider lang={params.locale}>
          {children}
        <LocaleContext.Provider/>
      </body>
    </html>
  );
}
```

そして、次のように Client Component で hooks を利用します。

```tsx:app/[locale]/blog/_components/client-component.tsx
"use client";

import { useTransition } from "./../../../i18n/client";

export default function ClientComponent() {
  const { t } = useTransition()
  return <div>{t('blog-home')}</div>
}
```

また、App Router で利用できる Server Components では hooks を利用できません。このため、Server Components で使う文言取得用の関数も別で定義します。

```ts:i18n/server.ts
import "server-only";

import { RESOURCES, Locale, i18nKey, isSupportLocale } from "./resources";

export const getTranslation = (locale: Locale) => {
  if (!isSupportLocale(locale)) {
    throw new Error(`Unsupported locale: ${locale}`);
  }
  return { t: (key: i18nKey) => RESOURCES[locale][key] };
};
```

そして、上記で定義した getTranslation を次のように利用します。

```tsx:app/[locale]/blog/pages.tsx
import { SUPPORTED_LOCALES } from "./../../i18n/resources";
import { getTransition } from "./../../i18n/server";

export async function generateStaticParams() {
  return SUPPORTED_LOCALES.map((locale) => ({ locale }));
}

export default async function BlogHome({ params }: { params: { locale: string } }) {
  const { t } = getTransition(params.locale)
  return <div>{t('blog-home')}</div>
}
```

## 移行した感想

上記の手順で App Router でもライブラリを導入することなく i18n の機能を移行できました。一方で、やはり Page Router の場合の方が i18n を簡単に導入できたので、今回の対応では次のような感想を持ちました。

- middleware の実装で楽をしたい
- Server Components の文言のローカライゼーション用の関数 (getTransition) は locale を引数に取るのが気になる

これらの課題を解決するライブラリがあるのか調べたところ、[next-translate](https://github.com/aralroca/next-translate) や [next-intl](https://next-intl-docs.vercel.app/) がありそうです。どちらのライブラリについても、middleware の実装は提供されている関数を利用して数行で実装でき、文言のローカライゼーション用の関数についても useTransition のような関数を ClientComponents と Server Components のどちらでも呼べるようになっています。

文言のローカライゼーション用の関数のインターフェイスをどうやって揃えるのかについて気になったので調べたところ、次の実装にあるようにビルド時に動的にコードを書き換えているようです。

https://github.com/aralroca/next-translate-plugin/blob/67e728a9d2881de3d4ccc15f40ecf9f89899415a/src/templateAppDir.ts

この実装方法では Webpack から Turbopack への移行の障害にもなってしまいそうなので、個人的にはライブラリを積極的に使いたくないな〜と思いました。

ということで、結果的にはこの i18n 対応の大変さや開発サーバーでのページの読み込みの遅さなどが気になって、App Router への移行の実装をまだ main ブランチにはマージしていません。Next.js 側での i18n サポートの改善に期待しつつ、しばらくはライブラリを利用しないで実装するのが良さそうだと思います。

---

追記

記事を書いてから気づいたのですが、次のライブラリはビルド時に動的なコード書き換えをしておらず、非常に薄くて使いやすいライブラリだなと感じています。

https://github.com/QuiiBz/next-international

## 追記：Server Components での locale のバケツリレーを回避する

記事内の `getTranslation` 関数は locale を引数にとるので、この関数を利用するには locale を page.tsx から該当のコンポーネントへ渡す必要があります。個人ブログのような規模が小さいコードでは問題ないのですが、それ以外の場合では locale のバケツリレーがかなり大変になりそうです。

この問題を回避する方法としては、まずは cookie や HTTP header を利用することを思いつきました。しかし、[Next.js では `cookies()` や `headers()` を Server Components で利用すると Dynamic Rendering になってしまう](https://nextjs.org/docs/app/building-your-application/rendering/server-components#switching-to-dynamic-rendering)ので、パフォーマンス的に良くありません。

そこで、[next-international のコードを参考にし](https://github.com/QuiiBz/next-international/blob/a049386f43bf2c42a2b3ed9b8d3e82b42ff9eebe/packages/next-international/src/app/server/get-locale-cache.tsx)、[React の cache 関数](https://ja.react.dev/reference/react/cache)を利用することでこの問題を回避しました。実際のコードは次のようになります。

```ts:i18n/server.ts
import "server-only";

import { cache } from "react";
import { RESOURCES, type i18nKey, isSupportLocale } from "./resources";

const getLocale = cache<() => { current: string | undefined }>(() => ({
  current: undefined,
}));

export const setStaticParamsLocale = (value: string) => {
  getLocale().current = value;
};

export const getTranslation = cache(async () => {
  const currentLocale = getLocale().current;
  if (!isSupportLocale(currentLocale)) {
    throw new Error(`Unsupported locale: ${currentLocale}`);
  }
  const resource = await RESOURCES[currentLocale]();
  return { t: (key: i18nKey) => resource[key] };
});
```

ここで定義した `setStaticParamsLocale` を page.tsx の先頭で呼び出した後で、`getTranslation` が利用できるようになります。

```tsx:app/[locale]/blog/pages.tsx
import { SUPPORTED_LOCALES } from "./../../i18n/resources";
import { getTransition } from "./../../i18n/server";

export async function generateStaticParams() {
  return SUPPORTED_LOCALES.map((locale) => ({ locale }));
}

export default async function BlogHome({ params }: { params: { locale: string } }) {
  setStaticParamsLocale(params.locale)

  const { t } = getTransition()
  return <div>{t('blog-home')}</div>
}
```
