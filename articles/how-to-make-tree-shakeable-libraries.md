---
title: "バンドルサイズに優しい tree shakeable なライブラリを作成する"
emoji: "🌲"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "typescript", "performance"]
published: false
---

:::message
この記事は [Cybozu Advent Calendar 2022](https://blog.cybozu.io/advent_calendar_2022) の 17 日目の記事になります。
:::

この記事では次のブログの内容を参考にしながら、バンドルサイズに優しい tree shakeable な JavaScript ライブラリの作成方法について紹介します。細かい内容が気になる方は、是非原文の方もご確認ください。

https://blog.theodo.com/2021/04/library-tree-shaking/

# 作成方法のまとめ

結論から知りたい人向けに、まずは対応方法のまとめから記述します。バンドルサイズに優しい tree shakeable な JavaScript ライブラリを作成するためには、次の点に気をつけると良いです。

- ESM 形式でライブラリを配布し、package.json の `module` フィールドを指定する
- ライブラリで使用する npm パッケージも可能な限り tree shakeable なものを利用する
- １つのファイルにバンドルせず、モジュールツリーの構造を保持したまま配布する
- package.json の `sideEffects` フィールドに `false` 指定する
- トップレベルで実行するコードに `/*#__PURE__*/` コメントを挿入する

# Tree shaking とは？

Tree shaking とは、モジュールの依存関係を静的に解析して、副作用のない不要なコードを削除する機能のことです。[^1] 昨今のモジュールバンドラーのほとんどは、tree shaking の機能をサポートしています。また、すでに本文では tree shakeable という単語が出てきていますが、「tree shakeable = tree shaking が行われる」くらいの意味で理解してもらえればと思います。

[^1]: 広義な意味では、dead code elimination と同じように感じるのですが、あえて異なる名前にしている理由については [Rich Harris さんがブログを書いて説明していました](https://medium.com/@Rich_Harris/tree-shaking-versus-dead-code-elimination-d3765df85c80)。

# Tree shakeable なライブラリ作成でやること

Tree shakeable なライブラリを作成をするためにやることは、次の 3 点になります。

- ECMAScript modules (ESM) 形式でライブラリを配布する
- モジュールツリーの構造を保持したまま配布する
- ライブラリに含まれる副作用の可能性を削減する

今回は、[Vite のライブラリモード](https://vitejs.dev/guide/build.html#library-mode)を利用した場合にフォーカスしながら、各対応について紹介したいと思います。他のビルドツールを利用している場合は、適宜設定などは読み替えて貰えればと思います。

# ESM 形式でライブラリを配布する

ライブラリを配布する際のモジュール形式としては、主に Universal Module Definition (UMD) 形式、CommonJS (CJS) 形式、ESM 形式などがあります。**Tree shaking が正しく行われるためには、ライブラリを ESM 形式で配布することが必要です。** Vite を使っている場合は、[build.lib](https://vitejs.dev/config/build-options.html#build-lib) の target の値を `es` にすることで、ビルド成果物が ESM 形式になります。

**また、CJS と ESM などの複数のモジュール形式でパッケージを配布する場合には、次のように package.json の `module` フィールドに ESM 形式でビルドされた成果物へのパスを設定する必要もあります。**[^2]

[^2]: 複数のモジュール形式で配布を行う場合の package.json の設定は、[こちらの記事](https://trap.jp/post/1666/)にもある通り歴史的経緯から難しいケースが多いので、[Packemon](https://packemon.dev/) などのツールを検討してもよいかもしれないです。

```json
{
  "main": "dist/libraryA.cjs.js",
  "module": "dist/libraryA.esm.js"
}
```

Vite が利用している rollup や webpack などの主要なバンドラーは、この `module` フィールドを解釈し、ライブラリが ESM 形式で配布されているかどうか判断しています。

<!-- TODO: details にするかどうか考える -->

CJS 形式で tree shaking が行われない理由としては、CJS で広く使われている `require` と `module.exports` が動的であるためです。`require` は、次のような条件式の中で呼び出すことが可能です。

```js
if (someCondition) {
  // 下記を import { moduleA } from "./libraryA"; とすることはできない
  const { moduleA } = require("./libraryA");
}
```

静的解析を行う tree shaking では、このようなランタイムでの評価が必要な動的なモジュールの依存関係を扱うことが困難です。このため、CJS で読み込まれているモジュールは基本的に全てバンドルされてしまいます。[^3]

[^3]: [こちらの記事](https://www.kabuku.co.jp/developers/tree-shaking-in-2018)によると、CJS の tree shaking に対応するバンドラーとして [Parcel](https://parceljs.org/) があるようです。

このことをふまえると、作成するライブラリも ESM 形式で配布されている npm パッケージに依存するのが望ましいです。**[bundlephobia](https://bundlephobia.com/) などを利用して、利用するパッケージが tree shakeable かどうかを確認するのがおすすめです。**

# モジュールツリーの構造を保持したまま配布する

ライブラリを配布する際には、1 つのファイルにバンドルすることも多いと思います。しかし、tree shaking のことを考えると、次のようなデメリットがあります。

- もともとのモジュールツリーの情報が失われ、静的解析のときに不利になる
- バンドル後のライブラリ内の依存関係の詳細な解析が難しい

ブログ内では、静的解析のときに不利になる事によって、CJS の npm パッケージを読み込んだ場合や code splitting を行う場合に効果的に tree shaking がされない例が紹介されています。[^4]

[^4]: 実際のサンプルコードを webpack や rollup を利用してビルドしながらの解説があるので、ぜひそちらを参考にしていただければと思います。

それぞれの現象については、本記事の参考にしている 「」の「」章で具体的なコードを実行しながら解説しています。

また、ビルドした JS の依存関係を可視化する場合にも、Vite だと [rollup-plugin-analyzer](https://github.com/doesdev/rollup-plugin-analyzer) などの結果を解析する際も、１つのファイルに

１つのファイルにバンドルされている場合は、次のようになります。

一方で、１つのファイルにバンドルしない場合は、次のようになります。

こちらの方が、ライブラリ内のモジュールの読み込みの有無が明確になり、バンドルサイズの解析もやりやすいです。

Vite を使っている場合には、rollup の [preserveModules オプション](https://rollupjs.org/guide/en/#outputpreservemodules) を利用することで、もともとのモジュールツリーの構造を保持したまま配布することができます。[ディスカッションに投稿されている内容](https://github.com/vitejs/vite/discussions/8098)を参考にした実際の設定は次のようになります。

```js
import { defineConfig } from "vite";

export default defineConfig({
  build: {
    ...,
    rollupOptions: {
      ...
      output: {
        preserveModules: true,
        preserveModulesRoot: "src",
        entryFileNames: ({ name: fileName }) => {
          return `${fileName}.js`;
        },
      },
    },
  },
  ...
});
```

# ライブラリに含まれる副作用の可能性を削減する

Tree shaking で正しく不要なコードが削除されるためには、削除対象のコードに副作用があるかどうかの判定が重要になります。副作用とは、[webpack での定義](https://webpack.js.org/guides/tree-shaking/#mark-the-file-as-side-effect-free)を引用すると次のようになります。

> A "side effect" is defined as code that performs a special behavior when imported, other than exposing one or more exports. An example of this are polyfills, which affect the global scope and usually do not provide an export.

副作用のあるコードの例としては、次のようなトップレベルで実行されるようなコードが含まれている場合などが挙げられます。また、引用にもあるように polyfill なども想像しやすいと思います。

```js
export const moduleA = "moduleA";

console.log(moduleA); // トップレベルで実行される
```

なるべく正確に副作用の解析を行ってもらうために、ライブラリを作成する際には次の 2 つ対応を行う必要があります。

- package.json の `sideEffects` フィールドに `false` 指定する
- トップレベルで実行するコードに `/*#__PURE__*/` コメントを挿入する

## package.json の `sideEffects` フィールドに `false` 指定する

バンドラーは、ライブラリ内の副作用を解析する際に、package.json の `sideEffects` フィールドを解釈します。この値を設定しないと、バンドラーはライブラリ内のモジュールについて常に副作用を持つ可能性があるとして解析を行います。

**副作用のあるコードを含まないライブラリについては、次のように `false` を指定します。これによって、バンドラーにモジュール単位での副作用がないことを明示できます。**

```json
{
  "name": "libraryA",
  "sideEffects": false
}
```

もし、ライブラリ内に副作用のあるコードを含む場合は、配列でファイル名を列挙します。列挙の仕方などは、[webpack のドキュメント](https://webpack.js.org/guides/tree-shaking/#mark-the-file-as-side-effect-free)が参考になります。

## トップレベルで実行するコードに `/*#__PURE__*/` コメントを挿入する

トップレベルで実行されるコードは副作用をもつと判断されますが、実際に tree shaking されても問題無いコードもあると思います。社内のライブラリでも、次のようなコードが原因で tree shaking されてほしいコードがバンドルされてしまうケースがありました。

```js
export const someContext = React.crateContext(null);
```

**このようなコードについては、次のように `/*#__PURE__*/` コメントを挿入します。これによって、バンドラーに文 (statement) 単位での副作用がないことを明示できます。**

```js
export const someContext /*#__PURE__*/ = React.crateContext(null);
```

実際に [react-redux](https://github.com/reduxjs/react-redux) などのライブラリでもこのような対応がされています。

https://github.com/reduxjs/react-redux/blob/8d03182d36abe91cb0cc883478f3b0c2d7f9e17f/src/components/Context.ts#L14-L15

このコメントについては、バンドラー内部で利用している minifier ([terser](https://github.com/terser/terser) など) が解釈し、副作用がないことを理解しています。

# まとめ

この記事では、バンドルサイズに優しい tree shakeable な JavaScript ライブラリの作成方法について紹介しました。最近だと、デザインシステムの重要性が高まって来たり、monorepo 開発のツールも多く登場している事もあって、社内で JavaScript ライブラリを開発することも多いと思います。開発しているライブラリのバンドルサイズが気になっていて、tree shaking について右も左もわからないと感じている方の参考になれば幸いです。

また社内ライブラリを使う際には、JS のバンドルサイズなどのパフォーマンスに関するメトリクスを定期的に計測をすることが重要です。バンドルサイズについては、Next.js の場合にはなりますが、PR ごとにバンドルサイズを評価してくれるアクションがあるのでおすすめです。

https://github.com/hashicorp/nextjs-bundle-analysis

ということ本ブログの内容は以上になります！良いライブラリ生活を！
