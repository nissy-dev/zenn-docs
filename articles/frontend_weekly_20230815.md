---
title: "Vercel による React Server Components の解説など: Cybozu Frontend Weekly (2023/08/15号)"
emoji: "🎆" # お好きな絵文字を
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["CybozuFrontendWeekly", "frontend"]
published: false
publication_name: "cybozu_frontend"
---

こんにちは! サイボウズ株式会社フロントエンドエンジニアの [nissy](https://twitter.com/nissy_dev) です。

# はじめに

サイボウズでは毎週火曜日に Frontend Weekly という「1 週間の間にあったフロントエンドニュースを共有する会」を社内で開催しています。

今回は、2023/08/15 の Frontend Weekly で取り上げた記事や話題を紹介します。

# 取り上げた記事・話題

## Understanding React Server Components

https://vercel.com/blog/understanding-react-server-components

Vercel による React Server Components についての解説記事です。RSC が解決する SSR の課題が何かという点から、具体的に使うときに Client Components とのバランスをどうするか？という内容まで書かれています。

## Bun 1.0 is coming

https://bun.sh/1.0

Bun の 1.0 のリリースが 来月の 9 月 8 日に予定されています。ここ最近のリリースでも様々な開発が進んでいたので、どんな発表になるのか楽しみです！

## Astro 2.10: Persistent State in View Transitions

https://astro.build/blog/astro-2100/

Astro の 2.10 がリリースされました。ViewTransitions において画面遷移時に再レンダリングを抑えるディレクティブが追加されました。またこのタイミングで Astro 3.0 の beta バージョンもリリースされています。

## Joy UI is now in Beta!

https://twitter.com/MUI_hq/status/1686406501525491712?s=20

Material UI が開発している React のコンポーネントライブラリ「Joy UI」がベータ版になりました。Joy UI は、Material UI とは異なり Material Design の仕様に合わないデザインにも対応できる UI コンポーネント集になっています。Material UI から移行しやすいようにコンポーネントの API もかなり揃えているようです。

## I’m joining @vercel as a Design Engineer

https://twitter.com/shadcn/status/1688945578439499776?s=20

Radix UI と Tailwind CSS で作られたスニペット集の [shadcn/ui](https://ui.shadcn.com/) の作者が Vercel にデザインエンジニアとしてジョインしました。

## pnpm/pacquet

https://github.com/pnpm/pacquet

Rust で Node.js のパッケージマネージャーを新たに実装しようとしているリポジトリです。少し前までは Node.js の TSC の [@yagiznizipli](https://twitter.com/yagiznizipli) さんが個人で開発していたのですが、この度 pnpm チームと一緒に開発をしていくことになりました。

ちなみに Rust で Node.js のパッケージマネージャーを実装する動きは他にもあり、Microsoft に所属している人が開発している orogene というツールがあります。

https://github.com/orogene/orogene

こちらはかなり実装が進んでおり、ベンチマーク結果では bun と同等の速さが出ているようです。

## Tracking issue: Performance improvement

https://github.com/fabian-hiller/valibot/issues/73

valibot と zod のパフォーマンスについて、様々なランタイムでベンチマークを取った issue が作成されました。この issue をきっかけに、valibot の作者はパフォーマンスの改善にいくつか着手しているようです。

## react-hook-form が Valibot に対応、バンドルサイズが 92%削減

https://zenn.dev/hayato94087/articles/f76c878bc97d65

React Hook Form の resolvers が valibot に対応し、バンドルサイズが非常に小さくなりました。

## esbuild 0.19

https://github.com/evanw/esbuild/releases/tag/v0.19.0

esbuild の 0.19 のリリースです。変数名を含む dynamic import をバンドルできるようになったり、エントリポイントの指定でワイルドカードが使えるようになったりするなどの更新がありました。

## Blogged Answers: My Experience Modernizing Packages to ESM

https://blog.isquaredsoftware.com/2023/08/esm-modernization-lessons/

Redux の開発者 [Mark Erikson さん](https://github.com/markerikson)が経験した、いろんなツールで動くようにライブラリを開発を動かすことの難しさについて書かれている記事です。CJS と ESM の問題にとどまらず、TypeScript、Webpack v4 なども考慮する必要があり、かなりの時間を費やしたということが書かれています。さらに、Next.js の React Server Components はさらに複雑性を持ち込もうとしており、不満があることにも触れられています。

## Vue.js エコシステム動向 2023

https://speakerdeck.com/kazupon/vue-dot-jsekosisutemudong-xiang-2023

Vue.js や Nuxt に関する最近の動向がまとまっている記事です。Vue.js についてあまり知らない人にもおすすめの記事になっています。今年の 10 月 28 日 には Vue Fes Japan 2023 も行われるようです！

## ViteConf 2023

https://viteconf.org/23/

Vite のオンラインカンファレンスが、10 月の 5 〜 6 日にかけて行われます。スピーカーやトークに関する情報はまだ公開されてないようですが、普段からお世話になっているユーザーとしては楽しみです！

## Bringing Sharp to WebAssembly and WebContainers

https://blog.stackblitz.com/posts/bringing-sharp-to-wasm-and-webcontainers/

ブラウザで Node.js を直接実行できる WebContainers において、画像変換ライブラリの [sharp](https://sharp.pixelplumbing.com/) が利用できるようになったという記事です。記事では、sharp を WebAssembly に移植する手順やその際に遭遇した問題点などについて詳しく書かれています。

## An Introduction to htmx, the HTML-focused Dynamic UI Library

https://www.sitepoint.com/htmx-introduction/

HTML からアクセスできる機能を活用しながら動的な UI を構築できる htmx を紹介している記事です。

# あとがき

今週の記事の中では、個人的に Mark Erikson さん の記事が印象的でした。ESM と CJS に始まる JS のモジュール解決が複雑になりすぎている問題は、かなり根深いように感じています。どうすればいいのか JS 全体で答えが出せていないなかで、ライブラリ開発者が消耗していくのだけは避けたいところです。

サイボウズでは毎月、最終火曜日の 17 時から Frontend Monthly というイベントを YouTube Live で開催しています。その月のフロントエンド注目ニュースや、ゲストを呼んでの対談など、フロントエンドに関する発信をしていますので是非どうぞ！

https://cybozu.github.io/frontend-monthly/
