---
title: "Storybook の : Cybozu Frontend Weekly (2023/11/21号)"
emoji: "" # お好きな絵文字を
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["CybozuFrontendWeekly", "frontend"]
published: true
publication_name: "cybozu_frontend"
---

こんにちは! サイボウズ株式会社フロントエンドエンジニアの [nissy](https://twitter.com/nissy_dev) です。

# はじめに

サイボウズでは毎週火曜日に Frontend Weekly という「1 週間の間にあったフロントエンドニュースを共有する会」を社内で開催しています。

今回は、2023/11/21 の Frontend Weekly で取り上げた記事や話題を紹介します。

# 取り上げた記事・話題

## @storybook-test: more streamlined and powerful testing

https://storybook.js.org/blog/storybook-test/

`@storybook/jest` と `@storybook/testing-library` を統合した `@storybook/test` を紹介しているブログです。
Storybook の interaction tests を書くときに必要な assert や spy に関する関数も統合され、内部では Vitest の関数を export するようになりました。

## How Core Web Vitals saved users 10,000 years of waiting for web pages to load

https://blog.chromium.org/2023/11/how-core-web-vitals-saved-users-10000.html

Core Web Vitals の導入によって起きたパフォーマンス改善に関する活動を紹介している記事です。bfcache, prerender などのブラウザそのものへの改善だけでなく、[Aurora Project](https://developer.chrome.com/aurora/) を通じた Web フレームワークとの連携なども行っているのが印象的でした。

## Announcing TypeScript 5.3 - TypeScript

https://devblogs.microsoft.com/typescript/announcing-typescript-5-3/

TypeScript 5.3 が正式にリリースされました 🎉 リリースには、import attributes の構文サポートなどが含まれています。

## Using CSS content-visibility to boost your rendering performance

https://blog.logrocket.com/using-css-content-visibility-boost-rendering-performance

レンダリングのパフォーマンスを改善する `content-visibility` プロパティに関するブログです。
`content-visibility` がどのようにパフォーマンスのスコアに影響を与えるのかについて詳しく解説されています。

## Last week, we shared StorybookGPT – a GPT that automatically creates stories for React TS components.

https://twitter.com/storybookjs/status/1726604084801872072

GPT を使って自動で React コンポーネント の story を作成するツールの紹介ツイートです！実際のツールは次のリポジトリで公開されています。

https://github.com/eduardconstantin/storybook-genie

## vuejs/core-vapor

https://github.com/vuejs/core-vapor

Vue の新しいコンパイルモードとして開発が進んでいる Vapor に関するリポジトリが公開されました。
Vapor については、公式ドキュメントでも以下のように言及されています。

> 仮想 DOM に依存せず、Vue の組み込みのリアクティビティーシステムをより活用する、Solid にインスパイアされた新しいコンパイル戦略（Vapor モード）も模索しています。

参考：https://ja.vuejs.org/guide/extras/reactivity-in-depth.html#connection-to-signals

## Announcing Vite 5

https://vitejs.dev/blog/announcing-vite5

Vite 5.0 が正式にリリースされました 🎉 内部で利用している Rollup のバージョンが v4 へ更新されたり、CJS 向けの Node.js API などが deprecated になったようです。バージョンを更新するための [migration guide](https://vitejs.dev/guide/migration.html) も公開されています。

## Deno Subhosting: the easiest and most secure way to run untrusted multi-tenant code

https://deno.com/blog/subhosting

信頼できない JS コードを安全に実行するための Deno Subhosting というサービスが公開されました。
製品内でユーザーが自由にカスタマイズやプラグインを作成できる B2B の SaaS 企業を主なターゲットとしたサービスのようです。

## Intent to Deprecate & Remove: Third-Party Cookies

https://groups.google.com/a/chromium.org/g/blink-dev/c/RG0oLYQ0f2I/m/xMSdsEAzBwAJ

Chromium でのサードパーティ Cookie の段階的な廃止についての最新動向です。2024 年の １ Q には 1% のユーザーに対してテストを行い、2024 年の 3Q に正式な段階的な廃止を始める予定のようです。

# あとがき

サイボウズでは毎月、最終火曜日の 17 時から Frontend Monthly というイベントを YouTube Live で開催しています。その月のフロントエンド注目ニュースや、ゲストを呼んでの対談など、フロントエンドに関する発信をしていますので是非どうぞ！

https://cybozu.github.io/frontend-monthly/
