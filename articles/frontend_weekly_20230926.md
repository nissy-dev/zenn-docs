---
title: "TypeScript のドキュメンタリーの公開など : Cybozu Frontend Weekly (2023/09/26号)"
emoji: "🎞️" # お好きな絵文字を
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["CybozuFrontendWeekly", "frontend"]
published: true
publication_name: "cybozu_frontend"
---

こんにちは! サイボウズ株式会社フロントエンドエンジニアの [nissy](https://twitter.com/nissy_dev) です。

# はじめに

サイボウズでは毎週火曜日に Frontend Weekly という「1 週間の間にあったフロントエンドニュースを共有する会」を社内で開催しています。

今回は、2023/09/26 の Frontend Weekly で取り上げた記事や話題を紹介します。

# 取り上げた記事・話題

## Node v20.6.0 (Current) | Node.js

https://nodejs.org/ja/blog/release/v20.6.0

Node.js v20.6.0 のリリースブログです。`.env` ファイルのビルトインサポートや `import.meta.resolve` がフラグなしで利用可能になる等の更新が行われています。

## Chakra, Panda and Ark - What's the plan?

https://www.adebayosegun.com/blog/chakra-panda-ark-whats-the-plan

Chakra UI と新しく開発している Panda CSS・Zag.js・Ark UI との関係性を整理しつつ、今後の開発の方向性などに関する多くの疑問点に答えている記事です。Chakra UI の v3 では、Next.js のサポートの改善、Panda CSS との連携の改善などを予定しています。

## なんで bun install は速いのか？

https://zenn.dev/laiso/scraps/9a787a6888e228

`bun install` の速さについて調査しているスクラップです。キャッシュの扱いや lockfile を生成するための npm API へのアクセスなどが他のパッケージマネージャーとの速度差に繋がっているようです。

## v0 by Vercel Labs

https://x.com/vercel/status/1702351846445080953?s=20

Vercel がテキストのプロンプトから UI を生成するサービスを開発中です。現在は private alpha になっており、利用したい人は Waitlist に登録する必要があります。

サイトでは、実際に生成された UI の見た目と Tailwind CSS でスタイリングされた React コンポーネントや HTML のサンプルが確認できます。

## Submit your proposals for Interop 2024

https://web.dev/interop-2024-proposals/

Interop 2024 の提案を 9/14〜10/5 の期間で募集しています。ブラウザ間の互換性向上の取り組みを誰でも提案できるので、興味のある人は提案してみてはいかかでしょうか。

## Next.js Conf

https://nextjs.org/conf

今年の Next.js Conf は 10/26 に行われます！

## TypeScript5.2 で追加された using Declarations and Explicit Resource Management をテストで活用する

https://blog.nnn.dev/entry/2023/09/14/110000

テストコードの改善のために TypeScript5.2 で追加された Explicit Resource Management を利用している記事です。`afterEach` で書いていたテストの後処理を `Symbol.dispose` メソッドと using 宣言を使って実装することで、カプセル化などの恩恵が得られる例を紹介しています。

## WebKit Features in Safari 17.0

https://webkit.org/blog/14445/webkit-features-in-safari-17-0/

Safari 17.0 に入った WebKit の新しい機能の紹介記事です。search 要素や popover API などが紹介されています。

## Remix v2

https://remix.run/blog/remix-v2

Remix の v2 が公開されました。このブログでは v2 で入る機能を簡単に紹介しつつ、React Server Components に対する今後の対応方針について記述されています。

## TypeScript Origins: The Documentary

https://www.youtube.com/watch?v=U6s2pdxebSo

TypeScript に関するドキュメンタリー動画が公開されました！[Anders Hejlsberg](https://twitter.com/ahejlsberg) さんを始めとした多くの関係者のインタビューによって構成されており、TypeScript が OSS としてどのように開発されていったのかがわかる内容になっています。

## Did the React team forget the React Forget compiler?

https://www.reddit.com/r/reactjs/comments/16nnh4z/comment/k1jbr4t/

React Forget の現状について、React コアチームの方からのコメントです。React Forget を設計する上での難しいポイントの１つとして、エイリアス解析を取り上げて解説しています。

## Deno vs. Bun vs. Node.js: A Feature Comparison

https://dev.hexagon.56k.guru/posts/deno-vs-bun-vs-node/

Deno、Bun、Node.js を比較をしている記事です。機能ごとのサポート状況がまとめられたテーブルが非常にわかりやすいです。

## Deno 1.37: Modern JavaScript in Jupyter Notebooks

https://deno.com/blog/v1.37

Deno 1.37 がリリースされました。このリリースでは、jupyter notebook との連携や VSCode 拡張の改善などが行われています。

## Introducing Web Sustainability Guidelines

https://www.w3.org/blog/2023/introducing-web-sustainability-guidelines/

W3C で [Web Sustainability Guidelines](https://w3c.github.io/sustyweb/) というレポートが公開されました。このレポートでは、Web サイトと製品をより持続可能にするための多くの推奨事項がまとめられています。

## Cloudflare Fonts: enhancing website font privacy and speed

https://blog.cloudflare.com/cloudflare-fonts-enhancing-website-privacy-speed/

Couldflare が Cloudflare Fonts というサービスを公開しました。Cloudflare Fonts を使うと Google Fonts をサイト独自のドメインから簡単に読み込めるようになり、パフォーマンスやプライバシーでの改善が期待できます。

## State of HTML 2023 now open!

https://lea.verou.me/blog/2023/state-of-html-2023/

State of JavaScript や State of CSS に続いて、今年は State of HTML も公開されたようです。HTML の知識の復習にもなると思うので、時間がある人は投票してみてはいかがでしょうか。

## BudouX: 読みやすい改行のための軽量な分かち書き器

https://developers-jp.googleblog.com/2023/09/budoux-adobe.html

読みやすい改行を実現するための BudouX というライブラリの紹介記事です。Chrome 119 以降では、`lang="ja"` かつスタイルシートで `word-break: auto-phrase` と指定された要素は BudouX によって折り返されるようになります。

余談ですが、BudouX は Frontend Expert チームのブログにも使っています！

https://github.com/cybozu/frontend-expert/pull/51

# あとがき

個人的には、TypeScript のドキュメンタリーが面白かったです。Web が普及することを常に見据えて、JavaScript のスーパセットであることに労力を惜しまなかったことが伝わってくる内容でした。TypeScript の OSS としての発展は、Microsoft 社内での OSS に対する認識を大きく変化させていたのも印象的でした。

サイボウズでは毎月、最終火曜日の 17 時から Frontend Monthly というイベントを YouTube Live で開催しています。その月のフロントエンド注目ニュースや、ゲストを呼んでの対談など、フロントエンドに関する発信をしていますので是非どうぞ！

https://cybozu.github.io/frontend-monthly/
