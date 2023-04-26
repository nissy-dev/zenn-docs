---
title: "パフォーマンスが改善された Vite 4.3 のリリースなど : Cybozu Frontend Weekly (2023-04-25号)" # 目立ったニュースを選ぶ
emoji: "⚡" # お好きな絵文字を
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["CybozuFrontendWeekly", "frontend"]
published: false
publication_name: "cybozu_frontend"
---

こんにちは！ サイボウズ株式会社フロントエンドエンジニアの [nissy](https://twitter.com/nissy_dev) です。

# はじめに

サイボウズでは毎週火曜日に Frontend Weekly という「1 週間の間にあったフロントエンドニュースを共有する会」を社内で開催しています。

今回は、2023/04/25 の Frontend Weekly で取り上げた記事や話題を紹介します。

# 取り上げた記事・話題

## Multiple stylesheets per file

https://github.com/w3c/csswg-drafts/issues/5629

`@sheet` という記法を使って、CSS ファイル内に複数の stylesheet を定義する提案。2020 年に作成されたこちらの提案が、3 週間ほど前に CSS の Working Group に受け入れられたようです。

## Announcing Vite 4.3

https://vitejs.dev/blog/announcing-vite4-3.html

https://sun0day.github.io/blog/vite/why-vite4_3-is-faster.html

Vite 4.3 がリリースされました。パフォーマンスが大きく改善されたようです。パフォーマンスの改善のためにやったことについては、[@sun0day500](https://twitter.com/sun0day500) さんのブログで解説されていました。地道な最適化の積み重ねになっていて必見です。

## Preparing for v2

https://remix.run/docs/en/1.15.0/pages/v2

Remix の v2 リリースに関するドキュメントです。File System Routing において、ディレクトリ構造を使うのをやめてフラット化することを予定している点が話題になりました。

## It's time to learn oklch color

https://keithjgrant.com/posts/2023/04/its-time-to-learn-oklch-color/

CSS で色を指定する関数の 1 つである `oklch()` の紹介記事です。 従来の `hsl()` と比較して、より人間の感覚に沿った色を指定できたり、指定できる色に制限がないことなどのメリットがあるようです。

## Shipping the biggest update for the components.

https://twitter.com/shadcn/status/1647984600797564928

[Radix](https://www.radix-ui.com/) と [Tailwind CSS](https://tailwindcss.com/) で作られたコンポーネントスニペット集である [shadcn/ui](https://ui.shadcn.com/) のアップデートがあり、Skelton や Calender などの新しいコンポーネントが追加されました。

## "What's next for Deno?" by Ryan Dahl (NodeCongress '23)

https://www.youtube.com/watch?v=LVEGRj3RZSA

Ryan Dahl さんによる Deno の今後についての発表です。Node.js 互換の機能、Deno KV、Deno Deploy などを live coding を交えて紹介していました。動画では 「Deno は Node.js と同じ目標に向かって開発を進めている」と繰り返し述べているのが印象的でした。

## SpiderMonkey Newsletter (Firefox 112-113)

https://spidermonkey.dev/blog/2023/04/14/newsletter-firefox-112-113.html

Firefox 112 - 113 の Nightly リリースで行われた SpiderMonkey における JavaScript と WebAssembly に関する作業の紹介記事です。React のような最近の Web Framework のパフォーマンスを改善するような最適化を行っているとの報告がありました。

# あとがき

今週は、Vite 4.3 のリリースが個人的に印象に残りました。OSS であるにも関わらず、チーム全体でここまで集中してパフォーマンスの改善に力を入れられるのはすごいと思いました。ブログで紹介されていた個々の改善でやったことについても、`startsWith`/`endsWith` が `===` より遅いなど勉強になる話が多かったです。

---

サイボウズでは毎月、最終火曜日の 17 時から Frontend Monthly というイベントを YouTube Live で開催しています。その月のフロントエンド注目ニュースや、ゲストを呼んでの対談など、フロントエンドに関する発信をしていますので是非どうぞ！

https://cybozu.github.io/frontend-monthly/

また、サイボウズでは一緒にサイボウズのフロントエンドをより良くする仲間を募集しています。興味ある方はこちら ↓ から！

**【フロントエンドエキスパート】**
サイボウズのフロントエンドを最高にするためのチームです。

https://cybozu.github.io/frontend-expert/

https://cybozu.co.jp/recruit/entry/career/front-end-expert.html

**【フロントエンドエンジニア（kintone アーキテクチャ刷新 PJ）】**
クラウドサービス「kintone」のフロントエンドを新しくしています。

https://cybozu.co.jp/recruit/entry/career/front-end-engineer-kintone.html
