---
title: '標準化に向けて進んでいる Web Neural Network API について調べてみた'
emoji: '🔎'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Chrome', 'Web', '機械学習']
published: false
---

今月、W3C に提出されていた Web Neural Network API (Web NN) が、Chromium で Intent to Prototype[^1]になりました。この記事では、Web NN が新たに Web 標準として追加されようとしている目的、追加される API の詳細や今後の動向について調査してみました。

[^1]: [こちらのブログ](https://shisama.hatenablog.com/entry/2019/01/24/075701)によると、仕様、設計が書かれている状態 (desing phase) を指すみたいです。

:::message
この記事は Cybozu Advent Calendar 2021 の 22 日目の記事になります。
:::

## この記事の３行まとめ

- Web NN は、各ハードウェアで提供される Neural Network の API をブラウザからも利用できるようにする
- Web NN の標準化が進むことで、ブラウザでの Neural Network モデルの推論が高速化が期待できる
- Web 開発者は、Web NN より高レベルな API として提案されている [Model loader API](https://github.com/webmachinelearning/model-loader/blob/main/explainer.md) の動向を追うと良さそう

## Web NN が提案された目的

Neural Network モデルの推論は、豊富な計算資源にアクセスできる Web サーバーで行うのが現状では一般的です。一方で、Web サーバーで行う推論については、ユーザーの個人データがサーバーに送られることによるプライバシーの問題や、ビデオ会議のノイズ除去などリアルタイム性の必要な場面ではネットワーク遅延の問題が生じたりと、いくつかの課題も存在していました。このような課題を解決する手段として、ブラウザでの推論の需要が高まっています。

ここでは、Web NN が提案された目的や背景について、説明していきます。

### ブラウザでの推論の現状

ブラウザで Neural Network モデルの推論を行うためのライブラリとしては、[TensorFlow.js](https://www.tensorflow.org/js) や [ONNX Runtime Web](https://onnxruntime.ai/) (ONNX.js) が有名です。どちらについても、**推論を行う際のバックエンド (数値計算を行うレイヤー) については、ブラウザが API として提供している `WASM` か `WebGL` / `WebGPU` が指定できます**。

`WASM` は CPU での実行になるので、GPU を利用する `WebGL` / `WebGPU` のほうが推論が高速になることが多いです[^2]。**バックエンドの最適化は、デバイスごとに行う必要があり**、実際に最適化を行っている例は今年の JS Conf でも紹介されていました。

https://jsconf.jp/2021/talk/case-study-of-production-development-of-a-client-side-ml-application-in-a-mobile-browser

[^2]: 小さなモデルでは、WebGL を実行するオーバーヘッドコストが無視できず、 WASM のほうがフォーマンスが優れるケースがあります。

### ハードウェア単位での最適化

このように、ブラウザでの Neural Network モデルの推論は、ブラウザが API を提供する数値計算のレイヤーでしか最適化が行えないのが現状です。一方で、Neural Network モデルの推論については、ハードウェア単位での最適化が盛んに行われています[^3]。Web NN の設計デザインのドキュメントには、以下のような固有のハードウェアに対して設計された Neural Network の API が紹介されています。

[^3]: 自分の馴染みのあるところだと、GPU ではなく [TPU](https://ja.wikipedia.org/wiki/%E3%83%86%E3%83%B3%E3%82%BD%E3%83%AB%E3%83%BB%E3%83%97%E3%83%AD%E3%82%BB%E3%83%83%E3%82%B7%E3%83%B3%E3%82%B0%E3%83%BB%E3%83%A6%E3%83%8B%E3%83%83%E3%83%88) を利用した数値計算も主流になりつつあったり、Preferred Networks さんが[深層学習に特化したチップ](https://www.preferred.jp/ja/projects/mn-core/)を作成したりしていることが思い浮かびます。

- Android NN API : https://developer.android.com/ndk/guides/neuralnetworks?hl=ja
- Chrome OS API : https://chromium.googlesource.com/chromiumos/platform2/+/master/nnapi
- Direct ML (Windows) : https://github.com/microsoft/DirectML
- OpenVINO (Linux) : https://docs.openvino.ai/latest/index.html
- MacOS/iOS ML Compute API : https://developer.apple.com/documentation/mlcompute/

**現在のブラウザでは上記のような API にアクセスできないので、ハードウェア単位での最適化の恩恵を受けられません。この課題に対して、Web NN はこれらの API をブラウザから呼び出せるようにして、ブラウザでも数値計算の適切な最適化を行うことが目標です。**

従来は、以下のように各ライブラリが、`WASM` や `WebGL` / `WebGPU` を使って数値計算を最適化していました。

![](/images/web-neural-network-api/prev-with-wasm.png)
_従来の WASM を使った推論、https://webmachinelearning.github.io/webnn-intro/ からの引用_
![](/images/web-neural-network-api/prev-with-webgl.png)
_従来の WebGL を使った推論、https://webmachinelearning.github.io/webnn-intro/ からの引用_

一方で、Web NN は各ハードウェアでの最適化を抽象化するレイヤーとなります。ライブラリは、Web NN が提供する API で Neural Network モデルを構築することで、各ハードウェアでの最適化に注力する必要性が少なくなります。これによって、不要な再実装による非効率化や不具合を減らすことができ、ライブラリの品質向上につながります。

![](/images/web-neural-network-api/webnn-api.png)
_WebNN を使った推論、https://webmachinelearning.github.io/webnn-intro/ からの引用_

このように、近年のハードウェア単位での Neural Network の数値計算の最適化に伴って、Web NN はソフトウェア開発とハードウェア開発の橋渡しとなるようにデザインされています。

:::details 実装の詳細について
Web NN API は、最終的には ハードウェア 固有の API を実行することになりますが、セキュリティの問題からレンダラープロセスから直接実行することはできません。このため、IPC を利用して、GPU プロセスに対象の API を実行させるようになっています。以下の図がイメージしやすいと思います。

![](/images/web-neural-network-api/implementation-details.png)
_[WebNN implementation in Chromium (Design Doc)](https://docs.google.com/document/d/1KDVuz38fx3SpLVdE8FzCCqASjFfOBXcJWj124jP7ZZ4/edit) からの引用_
:::

## Web NN の詳細

ここでは、Web NN の API の詳細や今後の動向について説明します。

ちなみに、API 自体は、以下の Playground 環境や[ユースケースの例](https://webmachinelearning.github.io/webnn-samples-intro/)も Web 上に公開されているので、興味のある人は触ってみると良いと思います。

https://webmachinelearning.github.io/webnn-samples/code/

### API の紹介

Web NN は、Neural Network モデルの演算処理の記述、演算処理をもとにした計算グラフ[^4]の構築、計算グラフをもとにした計算の実行などの API を提供します。以下がサンプルコードになります。

[^4]: ノードが各演算や入力変数に対応する有向グラフのこと。Neural Network モデルの数値計算を効率化するために必要なデータ構造であり、詳しくは[こちらのブログ](https://qiita.com/edo_m18/items/7c95593ed5844b5a0c3b)などを参考にしてもらえると助かります。

```ts
// 0. 初期化処理
const context = navigator.ml.createContext();
const builder = new MLGraphBuilder(context);
// 1. Neural Network モデルの演算処理の記述
// 以下の例だと、'C = 0.2 * A + B' という処理を記述している
const operandType = { type: 'float32', dimensions: [2, 2] };
const constant = builder.constant(0.2);
const A = builder.input('A', operandType);
const B = builder.input('B', operandType);
const C = builder.add(builder.mul(A, constant), B);
// 2. 定義した演算処理に対する計算グラフの構築
const graph = builder.build({ C: C });
// 3. 入力と出力を定義して計算の実行
const bufferA = new Float32Array(4).fill(1.0);
const bufferB = new Float32Array(4).fill(0.8);
const bufferC = new Float32Array(4);
const inputs = { A: bufferA, B: bufferB };
const outputs = { C: bufferC };
graph.compute(inputs, outputs);
// 4. 計算結果の確認
console.log('Output value: ' + bufferC);
```

ユーザーは、Neural Network モデルの演算処理を API を使って記述し、その記述をもとに計算グラフが構築され、計算が実行されます。

上記のコードは、Neural Network モデルを構築した人ならなんとなく見覚えがあるのではないでしょうか。Web NN は、上記のように機械学習ライブラリ用の低レベルの API を提供することにフォーカスしています。よって、以下については標準化の対象となっていません。

- モデルのシリアライズ形式の定義
- 暗号化やコンテンツ保護などのモデルの配信方法の詳細
- モデルの入力形式の定義

モデルのシリアライズ形式などは、Web NN とセットで提案されている [Model loader API](https://github.com/webmachinelearning/model-loader/blob/main/explainer.md)[^5] で議論されることになりそうです。

[^5]: Web 開発者向けの高レベルな API が設計されており、多くのユーザーが実際に使うことになるのはこちらの API だと思います。

### 今後の動向

Web NN は、Web machine learning という Working グループが２年前に発足し、今に至っているようです。今後の WebNN の方針としては、以下のようなことが挙げられています。

- Origin Trial[^6] は Chrome OS で行う
- Candidate Recommendation[^7] は 2022 年 Q2 を予定
- 将来的にはブラウザでもモデルの学習を行えるように考えている

[^6]: [こちらのブログ](https://blog.jxck.io/entries/2016-09-29/vender-prefix-to-origin-trials.html#origin-trials)によると、特定のオリジンにだけ、実験中の機能を、期限付きで提供することのようです。
[^7]: [こちらの記事](https://qiita.com/nishiyamakanako/items/d67511ea74e2cb4eb071#%E6%A8%99%E6%BA%96%E5%8C%96%E3%81%AE%E3%83%97%E3%83%AD%E3%82%BB%E3%82%B9)によると、より広いコミュニティに対してシグナルを送って、実装も含めたレビューを実施してもらい、その情報を収集する段階のようです。

## まとめ

TODO

## 参考文献

- [Intent to Prototype: Web Neural Network API (WebNN)](https://groups.google.com/a/chromium.org/g/blink-dev/c/PD6TDMDS9mg/m/N3MrigMyCAAJ)
- [Web Neural Network API Explained](https://github.com/webmachinelearning/webnn/blob/main/explainer.md)
- [Intent to Prototype: Web Machine Learning: Model Loader API](https://groups.google.com/a/chromium.org/g/blink-dev/c/w7Oq2_x2D8U/m/8BhYLGCIBwAJ)
- [Web ML Model Loader API Explainer](https://github.com/webmachinelearning/model-loader/blob/main/explainer.md)
- [WebNN implementation in Chromium (Design Doc)](https://docs.google.com/document/d/1KDVuz38fx3SpLVdE8FzCCqASjFfOBXcJWj124jP7ZZ4/edit)
- [Web Machine Learning | Enable efficient ML inference in the browser](https://webmachinelearning.github.io/)
