---
title: "SWC plugin を作成して Next.js に導入してみた"
emoji: "🐧"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "babel", "nextjs", "swc"]
published: false
---

Next.js の v12.2.0 では、SWC plugin がサポートがされました 🎉

https://nextjs.org/blog/next-12-2

元々 Babel plugin や ESLint plugin などを作るのが好きで、これを機に SWC plugin を作成して Next.js に適用してみたので、それについて記事を残そうと思います。

https://github.com/nissy-dev/swc-plugin-react-native-web

## 作成する SWC plugin

今回は、[babel-plugin-react-native-web](https://github.com/necolas/react-native-web/tree/master/packages/babel-plugin-react-native-web) を SWC plugin に置き換えて、[Next.js の React Native for Web 用の公式サンプル](https://github.com/vercel/next.js/tree/canary/examples/with-react-native-web)を動かせるようにすることを目標にしました。babel-plugin-react-native-web は、次のような import/export 文の変換を行うプラグインです。

```diff js
+ import ReactNative from "react-native"; // 変換前
- import ReactNative from "react-native-web/dist/index"; // 変換後

+ import { View, Text } from "react-native"; // 変換前
- import View from "react-native-web/dist/exports/View"; // 変換後
- import Text from "react-native-web/dist/exports/Text"; // 変換後

+ export { View } from "react-native"; // 変換前
- export { default as View } from "react-native-web/dist/exports/View"; // 変換後
```

このプラグインに決めた理由としては、React Native for Web の SWC plugin の需要がありそうなことを偶然 Next.js の discussion で見つけたことと、babel-plugin-react-native-web の実装が比較的軽かったことが大きな理由です。

https://github.com/vercel/next.js/discussions/36566

## SWC plugin の作成

### プロジェクトの作成

SWC は、プラグインのためのテンプレートプロジェクトを作成することができる CLI を用意しています。SWC plugin を作る際には、まず[こちらのドキュメント](https://github.com/swc-project/swc/tree/main/crates/swc_plugin#writing-a-plugin)通りにコマンドを実行します。

```sh
// if you haven't, add build targets for webassembly
rustup target add wasm32-wasi wasm32-unknown-unknown

cargo install swc_cli

swc plugin new swc-plugin-react-native-web --target-type wasm32-wasi
```

### テストの実装

プロジェクトが作成できたら、テストの実装を始めます。SWC の [`swc_ecma_transforms_module` のテストコード](https://github.com/swc-project/swc/tree/36d960c7e908f6924ce9b0fd23ce4cd972dbbba0/crates/swc_ecma_transforms_module/tests)を参考にしたところ、テストについては大きく２つの実装方法があるようです。

- 文字列でテストの入出力を用意して実行するテスト (`swc_ecma_transforms_testing` の `test!` マクロを利用)
- テストの入出力を記述した js ファイルを用意して実行するテスト (`swc_ecma_transforms_testing` の `test_fixture!` マクロを利用)

今回は、テストの入出力を記述したファイルを準備するのが少し手間だったので、`src/lib.rs` に `test!` マクロを利用する方法で実装しました。実装の際には、「[現実の Babel プラグインを SWC プラグインに移行する](https://zenn.dev/sosukesuzuki/articles/e6ac87acdd7122)」で紹介されている [swc-plugin-valtio](https://github.com/sosukesuzuki/swc-plugin-valtio) を参考にしました。

Cargo.toml にテストに必要な crate を追加します。(バージョンは適宜読み替えてください。)

```toml
[dev-dependencies]
swc_ecma_parser = "0.105.6"
swc_ecma_transforms_testing = "0.91.0"
```

追加した crate を使って、実際に次のようなテストコードを書いていきます。

```rust
#[cfg(test)]
mod transform_visitor_tests {
    use swc_ecma_parser::{EsConfig, Syntax};
    use swc_ecma_transforms_testing::test;

    use super::*;

    fn transform_visitor() -> impl 'static + Fold + VisitMut {
        as_folder(TransformVisitor::new())
    }

    test!(
        Syntax::Es(EsConfig {
            jsx: true,
            ..Default::default()
        }),
        |_| transform_visitor(),
        test_sample,
        // 変換前
        r#"
        import ReactNative from "react-native";
        import { View, Text } from "react-native";
        export { View } from "react-native";
        "#,
        // 変換後
        r#"
        import ReactNative from "react-native-web/dist/index";
        import View from "react-native-web/dist/exports/View";
        import Text from "react-native-web/dist/exports/View";
        export { default as View } from "react-native-web/dist/exports/View"
        "#
    );
}
```

テストケースについては、babel-plugin-react-native-web に記述されているテストと同じものを用意しました。

### プラグインの実装

テストが作成できたら、実際のプラグインの実装を `src/lib.rs` に記述していきます。実装に関しては、基本的には JS で記述されている Babel plugin の処理を Rust に置き換えていくのですが、**Babel plugin で利用できる path.replaceXXX 系の関数が SWC に用意されていないのが結構大変です。** babel-plugin-react-native-web でも、変換前の AST ノードを変換後の複数の AST ノードで置き換える `path.replaceWithMultiple` が利用されてました。これについては [swc-plugin-valtio](https://github.com/sosukesuzuki/swc-plugin-valtio) の実装を参考に、[visit_mut_module](https://docs.rs/swc_ecma_visit/latest/swc_ecma_visit/trait.VisitMut.html#method.visit_mut_module)を利用して実装しました。具体的な実装の流れは次の通りです。

- "react-native" を読み込む import/export 文に関する AST ノードを探す
- 対象のノードがあれば、変換後の AST ノードを作成し、対象ノードの index と変換後の AST ノードの配列を HashMap で保持しておく
- HashMap を利用して import/export 文の置き換えを行う

また、 babel-plugin-react-native-web では CommonJS 向けに import パスを変更できるオプションがあります。このオプションについては、styled-components の SWC plugin を参考に実装しました。

- [オプションの構造体の定義](https://github.com/vercel/next.js/blob/c83f94cf8bd24ca77136793cc772db116403e366/packages/next-swc/crates/styled_components/src/lib.rs#L19-L51)
- [serde_json を使った構造体へのデシリアライズ処理](https://github.com/swc-project/plugins/blob/601ffe76d9d5309b5ef455fc15217278c7ca5dfc/packages/styled-components/src/lib.rs#L11-L12)

```rust
#[derive(Debug, Default, Clone, Deserialize)]
#[serde(deny_unknown_fields)]
pub struct Config {
    #[serde(default)]
    pub commonjs: bool,
}

#[plugin_transform]
pub fn process_transform(program: Program, metadata: TransformPluginProgramMetadata) -> Program {
    let mut visitor = TransformVisitor::new();
    // metadata.plugin_config に文字列のオプションが渡されているので、
    // serde_json を使って構造体へデシリアライズしている
    let config = serde_json::from_str::<Config>(&metadata.plugin_config)
        .expect("invalid config for swc-plugin-react-native-web");
    // visitor にオプションを渡す
    visitor.set_config(config.commonjs);
    program.fold_with(&mut as_folder(visitor))
}
```

その他には、`JsWord` の型や `Box<T>` に関する match 式など SWC plugin に関する Tips をまとめた Plugin cheatsheet を参考にすることも多かったです。

https://swc.rs/docs/plugin/ecmascript/cheatsheet

### リリース

テストを全て通しプラグインの実装が終わったら、次のコマンドでソースコードを WASM にコンパイルします。

```sh
cargo prepublish --release
```

prepublish には、プロジェクト作成時に以下の alias が `.cargo/config` に登録されています。

```toml
[alias]
prepublish = "build --target wasm32-wasi"
```

生成された WASM は、`target/wasm32-wasi/release/your_plugin_name.wasm` に配置されているので、そのファイルを package.json の `main` と `files` フィールドに追加して、npm publish を行います。

## Next.js で SWC plugin を利用する

npm に publish した SWC plugin を利用するには、次のように next.config.js を編集します。

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  ...,
  experimental: {
    swcPlugins: [
      ["@nissy-dev/swc-plugin-react-native-web", { commonjs: true }],
    ],
  },
};
```

swcPlugins は、第一要素にプラグインのパス、第二要素にプラグインのオプションを含むタプルの配列を受け取ります。プラグインのパスは、npm モジュールのパッケージ名か、.wasm バイナリ自身への絶対パスが利用できます。プラグインのオプションは、何も設定しない場合でも空オブジェクトを渡す必要がありました。詳細は、次のドキュメントに記載されています。

https://github.com/vercel/next.js/blob/canary/docs/advanced-features/compiler.md#swc-plugins-experimental

## 最後に

この記事では、SWC plugin の作成方法と Next.js への導入の仕方を紹介しました。細かい実装については、実際のリポジトリのコードを参考にしてもらえると嬉しいです。

ちなみに、 React Native for Web 周りだと react-native-reanimated の Babel plugin も SWC plugin に移行したいとの声が上がっています。

https://github.com/software-mansion/react-native-reanimated/discussions/2586

https://twitter.com/axeldelafosse/status/1493992830175481859

約 800 行もある複雑な Babel plugin ですが、Babel plugin と Rust に慣れている方は是非挑戦してみると良いかもしれません！

https://github.com/software-mansion/react-native-reanimated/blob/main/plugin.js
