---
title: "バンドルサイズに優しい Tree shakeable なライブラリを作成する"
emoji: "🌲"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "typescript", "performance", "react"]
published: false
---

この記事では次のブログの内容を参考にしながら、バンドルサイズに優しい tree shakeable な JavaScript ライブラリの作成方法について紹介します。細かい内容が気になる方は、是非原文の方もご確認ください。

https://blog.theodo.com/2021/04/library-tree-shaking/

:::message
この記事は [Cybozu Advent Calendar 2022](https://blog.cybozu.io/advent_calendar_2022) の 17 日目の記事になります。
:::

## Tree shakeable なライブラリ作成

Tree shakeable なライブラリを作成をするためにやるべきことは、次の 3 点になります。

- ECMAScript modules (ESM) 形式でライブラリを配布する
- １つのファイルにバンドルせず、モジュールツリーの構造を保持したまま配布する
- ライブラリに含まれる副作用のあるコードを削減する

今回は、[Vite のライブラリモード](https://vitejs.dev/guide/build.html#library-mode)を利用した場合にフォーカスしながら、各対応について紹介したいと思います。他のビルドツールを利用している場合は、適宜設定などは読み替えて貰えればと思います。

### ESM 形式でライブラリを配布する

ライブラリを配布する際のモジュール形式としては、主に Universal Module Definition (UMD) 形式、CommonJS (CJS) 形式、ESM 形式などがあります。**Tree shaking が正しく行われるためには、ライブラリを ESM 形式で配布することが必要です。** Vite を使っている場合は、[build.lib](https://vitejs.dev/config/build-options.html#build-lib) の target の設定を `es` にすることで、ビルド成果物が ESM 形式になります。

**また、CJS と ESM などの複数のモジュール形式でパッケージを配布する場合には、次のように package.json の `module` フィールドに ESM 形式でビルドされた成果物へのパスを設定する必要もあります。**[^1]

[^1]: 複数のモジュール形式で配布を行う場合の package.json の設定は、[こちらの記事](https://trap.jp/post/1666/)にもある通り歴史的経緯から難しいケースが多いので、[Packemon](https://packemon.dev/) などのツールを検討してもよいかもしれないです。

```json
{
  "main": "dist/libraryA.cjs.js",
  "module": "dist/libraryA.esm.js"
}
```

Vite が利用している Rollup や Webpack などの主要なバンドラーは、この `module` フィールドを独自で解釈し、ライブラリが ESM 形式で配布されているかどうか判断しています。

<!-- TODO: details にするかどうか考える -->

UMD や CJS 形式で tree shaking が行われない理由としては、CJS のモジュールの import / export で広く使われている `require` と `module.exports` が動的であるためです。`require` は、次のような条件式の中で呼び出すことが可能です。

```js
if (someCondition) {
  // import { moduleA } from "./libraryA"; とすることはできない
  const { moduleA } = require("./libraryA");
}
```

モジュールの依存グラフの静的解析を行う Tree shaking では、このようなランタイムでの評価が必要な動的なモジュールの依存関係を扱うことができません。このため、CJS で読み込まれているモジュールは全てバンドルされてしまいます。

このことをふまえると、作成するライブラリも ESM 形式で配布されている npm パッケージに依存するのが望ましいです。**[bundlephobia](https://bundlephobia.com/) などを利用して、利用するパッケージが tree shakeable かどうかを確認するのがおすすめです。**

### モジュールツリーの構造を保持したまま配布する

ライブラリを配布する際には、1 つのファイルにバンドルする

- code splitting じでもちゃんと tree shaking がきく
- でバックが楽
- Vite の設定

https://github.com/vitejs/vite/discussions/8098

### ライブラリに含まれる副作用のあるコードを削減する

- 副作用のあるコードとは？判定方法
- sideEffect: false を設定する
- React でよくあるケースを紹介し、対処方法を提示

副作用のあるコードとは、

React コンポーネントのようなライブラリを作る場合には、
Styled component を使った

##

## 参考文献

- https://medium.com/@rjayakumarreddy887/create-a-ui-component-library-that-is-tree-shakable-cffbbc4d1829
- https://github.com/styled-components/babel-plugin-styled-components/issues/245
- https://github.com/Rich-Harris/agadoo
- https://www.youtube.com/watch?v=qtuFoyY0tyw
- https://github.com/reduxjs/react-redux/blob/8d03182d36abe91cb0cc883478f3b0c2d7f9e17f/src/components/Context.ts#L14-L15
- https://zenn.dev/mizchi/articles/bundle-size-with-treeshake
- https://github.com/hashicorp/nextjs-bundle-analysis
- ["module"フィールド対応](https://dwango-js.github.io/performance-handbook/startup/module-field/)
- [パッケージは Bundle を配布しない](https://dwango-js.github.io/performance-handbook/startup/reduce-bundle/)
