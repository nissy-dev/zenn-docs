---
title: "Web Neural Network API について調べてみた"
emoji: "👋"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

読むドキュメント

- Explaner
  - https://github.com/webmachinelearning/model-loader/blob/main/explainer.md
  - https://github.com/webmachinelearning/webnn/blob/main/explainer.md
- Tweet
  - https://twitter.com/intenttoship/status/1465866773866106880?s=20
  - https://twitter.com/intenttoship/status/1466857766241419273?s=20
- https://www.w3.org/TR/webnn/
  - W3C に提出されたドラフト
  - https://twitter.com/intenttoship/status/1465866773866106880?s=20
- https://github.com/webmachinelearning/webnn/blob/main/explainer.md
  - 概要
- Chronium のデザイン doc
  - https://docs.google.com/document/d/1KDVuz38fx3SpLVdE8FzCCqASjFfOBXcJWj124jP7ZZ4/edit
- Intent To Prototype
  - https://groups.google.com/a/chromium.org/g/blink-dev/c/PD6TDMDS9mg
- ライブラリ集
  - https://blog.front-end.ai/entry/2021/07/07/190000
- JS conf
  - https://docs.google.com/presentation/d/1fwqrHrEEcNBeLYkiy49r5qGx83UXpX705E31GbyAy3k/edit#slide=id.g1047b22436d_2_101

紹介されているベースラインモデル

- https://github.com/microsoft/DNS-Challenge/tree/master/NSNet2-baseline

今月、W3C に提出されていた Web Neural Network API (Web NN) が、Chromium で Intent to Prototype[^1]になりました。この記事では、Web NN が新たに Web 標準として追加されようとしている目的と、追加される API の詳細や今後の動向について紹介します。

[^1]: [こちらのブログ](https://shisama.hatenablog.com/entry/2019/01/24/075701)によると、仕様、設計が書かれている状態 (desing phase) を指すみたいです。

## Web NN API が提案された目的

近年、

### ブラウザでの推論の現状

### ハードウェア単位での最適化

## Web NN API

### API の紹介

### 今後の動向

## WebNN API とは

- ブラウザ上で、計算グラフを構築し、実行する API のこと
- 計算グラフとは？
  - 深層学習のモデル内部で起きている計算をグラフ上に表現したもの
    - ノードは演算（ops）と入力変数に対応する有向グラフである
  - ユーザーは、処理の流れを定義し、内部的にグラフが構築される
  - 計算グラフの構築の仕方
    - define by run (データを流す際に動的に構築)
    - define and run (データを流す前に事前に構築)
- モデルを構築した人なら一度は見たことのある、Conv2D や GRU などもサポートしている
  - ネットワークに必要な汎用的な関数は、高レベルままサポートするように決定した
    - 低レベルの API をサポートしている
  - 各 OS の API もこれらの API をサポートしてるため、そのまま呼び出せるように
    - 不要な再実装による非効率化やバグを減らす

## WebNN API の目的？

- ブラウザで推論できると何が嬉しいのか
  - プライバシー的な問題を解消できる、サーバーにデータは送らない
  - ネットワーク遅延の影響を削除できる
    - ビデオ会議のノイズ除去などリアルタイム性の必要なツールには必要
- 従来のバックエンドは、ブラウザが呼び出せる WebGPU / WebGL or WASM のみ
  - Tensorflow.js
    - https://www.tensorflow.org/js/guide/platform_environment
    - WASM -> CPU での最適化
    - WebGPU / WebGL -> GPU での最適化
    - ちなみにどっちが良い？
      - WebGL は、ほとんどのモデルで WASM よりも高速
      - 小さなモデルでは、WebGL を実行するオーバーヘッドコストが無視できず、 WASM のほうがフォーマンスが優れるケースが多い
  - ONNX.js
    - 今は、ONNX Runtime Web になったらしい
    - https://github.com/microsoft/onnxruntime/tree/master/js/web
    - これも OS によらず、WASM or WebGL の選択肢
- 一方で、OS ごとの最適化や ML に特化したプロセッサーでの最適化も進んでいる
  - Android NN API : https://developer.android.com/ndk/guides/neuralnetworks?hl=ja
  - Chrome OS API : https://chromium.googlesource.com/chromiumos/platform2/+/master/nnapi
  - Direct ML (Windows) : https://github.com/microsoft/DirectML
  - OpenVINO (Linux) : https://docs.openvino.ai/latest/index.html
    - 機械学習に特化した独自のプロセッサの元で高速化
  - macOS/iOS ML Compute API
    - https://developer.apple.com/documentation/mlcompute/
- これらの API もブラウザから呼び出せるようにして、ブラウザでも適切な最適化を行いたいということに見える
  - この図がわかりやすそう : https://webmachinelearning.github.io/webnn-intro/
  - ソフトウェアとハードウェアの開発の橋渡しとなるようにデザインされている
    - ライブラリ (ソフトウェア) が、各々ハードウェアの最適化するのは非効率
    - 数値計算の部分を抽象化した API をつかう
- そもそも ML は、ソフトウェアだけではなくハードウェアレベルでの最適化はかなり技術革新が進んでいる
  - ハードはかなり研究が盛んなイメージ
    - ここらへんは、PFN も力を入れている (半導体チップ)
    - https://www.preferred.jp/en/projects/mn-core/
  - 利用する際に、バックエンドチューニングは必須
    - https://docs.google.com/presentation/d/1fwqrHrEEcNBeLYkiy49r5qGx83UXpX705E31GbyAy3k/edit#slide=id.g1047b22436d_2_101

### WebNN API で取り扱わないところ

- モデルのシリアライズ形式の定義
  - ONNX が標準化を行っている
  - 一方で、TF/Pytorch も通常はそれぞれ独自のフォーマットで
- 暗号化やコンテンツ保護などのモデルの配信方法の詳細
- モデルの入力形式の定義

JS ML frameworks 用の低レベルの API を提供することにフォーカスしている

## その他の情報

- Web machine learning という Working グループがある
  - ２年前から動いている
  - https://webmachinelearning.github.io/webnn-intro/
  - 多くの事例が紹介されている
  - Editor もあるよ
    - https://webmachinelearning.github.io/webnn-samples/code/
- Polyfill が公開されていて、Tensorflow.js ベースの API が利用できる
  - https://github.com/webmachinelearning/webnn-polyfill
- Training -> No, but possible in future
  - 将来的にはブラウザで学習できるように考えてもいるらしい
- 代替案
  - WebGL と WebGPU の基盤で最適化する
    - OS 内部で利用可能なハードウェア固有の最適化や特別な命令を利用することができない
      - ハードウェアの進化が激しい現状厳しい
        - そもそも環境問題の話も聞くし、効率の良いハードが出たほうがいいのは間違いない
    - ハードウェアの多様性をうまく吸収するのは OS なので、OS が提供する API を使うのがよい
      - ハードウェアの違いによる不具合などを減らせそうで、フレームワークの品質向上につながる
- Candidate Recommendation は 2022 年 Q2 を予定 (来年)
  - より広いコミュニティに対してシグナルを送って、実装も含めたレビューを実施してもらい、その情報を収集
  - https://qiita.com/nishiyamakanako/items/d67511ea74e2cb4eb071#%E6%A8%99%E6%BA%96%E5%8C%96%E3%81%AE%E3%83%97%E3%83%AD%E3%82%BB%E3%82%B9
- Origin Trial は、Chrome OS で行われる
- 実装の詳細
  - レンダラープロセス -> Web NN API を叩く
  - Web NN API は、ハードウェアの最適化が得られる GPU プロセスで処理を呼び出す
  - そこでの処理は、各 OS プラットフォームの API を元にした処理
  - GPU プロセスとレンダラープロセスのやり取りは IPC で行われる

## Model loader API

- 目的
  - 外部のモデルフォーマットを読み込めるような、web developer 向けの API の提供
- TF Lite フラットバッファフォーマットをサポートすることをまずは検討
  - ただ Web 標準を満たしていないので、別のフォーマットを今後は検討している
  - さらに今後は、多くのモデルフォーマットを Web 標準に変換するこんばーたが必要になる
- ML モデルを保護する方法はある？
  - 現状はサポートを考えてはないが、不可能ではない
  - 案１：JS からは読めない、URL スキームの作成
- Chrome/TensorFlow, Intel, Microsoft/ONNX, Salesforce などが興味を示している

### TODO

- ユーザーの関心、フレームワークの関心を分けると良さそう
- こんなに最適化する必要あるの...?
- 計算グラフを Pytorch で書いたのと比較したい

## リンク

-
