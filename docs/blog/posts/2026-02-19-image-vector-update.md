---
date: 2026-02-19
categories:
  - Projects
  - PoC
  - AI
---

# Image Vector プロジェクト進捗: 顔認識、ブラウザ実行、そして SigLIP 2

前回 [2026-02-13](../../projects/image-vector-poc.md) での報告以降、Image Vector プロジェクトでは新たな可能性を探るためにいくつかの重要な実験を行いました。今回はその進捗と成果について共有します。

## 1. 顔認識機能の到達点

まず、顔認識（Face Recognition）の実装について検証を行いました。

- **実験ノート**: [58-face-recognition-summary.ipynb](https://github.com/cmscom/image-vector-poc/blob/main/notebooks/58-face-recognition-summary.ipynb)

結論として、現在のOSSモデルを利用した顔認識機能は **非常に高いレベル** に達していることが確認できました。
CMSにおいて「誰が写っているか」を自動でタグ付けしたり、特定の人物の写真を瞬時に検索したりする機能の実装において、現在のモデルは十分実用的な性能を持っています。これにより、単なる「類似画像検索」だけでなく、「人物検索」という強力な軸をシステムに組み込む準備が整いました。

## 2. Python と JavaScript の架け橋: Transformers.js

次に、アーキテクチャ面での大きな収穫です。ONNX Runtime と Transformers.js を用いた検証を行いました。

- **実験ノート**: [41-clip-l-js-evaluation.ipynb](https://github.com/cmscom/image-vector-poc/blob/main/notebooks/41-clip-l-js-evaluation.ipynb)

重要な発見は、**Transformers.jsを使ったJSでの実装も十分に可能である** という点です。具体的には、**Python (PyTorch) で生成した画像ベクトルと、JavaScript (Transformers.js) で生成したテキスト検索クエリの埋め込みベクトルが完全な互換性を持つ** ことが確認されました。

これは、重い画像処理やインデックス作成はサーバーサイド（Python）でバッチ処理しておき、ユーザーが検索窓に入力したキーワードのベクトル化は、サーバーを介さずにユーザーのブラウザ上（JavaScript）で完結させるという構成が可能であることを意味します。サーバー負荷を抑えつつ、高速な検索体験を提供できる可能性があります。

## 3. モデルの再考: SigLIP 2

最後に、より高性能なモデルとして **SigLIP 2** の検証を行いました。

- **実験ノート**: [68-siglip2-large-384-onnx.ipynb](https://github.com/cmscom/image-vector-poc/blob/main/notebooks/68-siglip2-large-384-onnx.ipynb)

検証の結果、**SigLIP 2 は前モデルと比較しても明確に良くなっている** ことが確認できました。特に画像のニュアンスを捉える能力や、多言語での検索精度において期待が持てます。これを ONNX 化して利用することで、推論速度とモデルサイズのバランスを取りながら、より高精度な検索システムを構築できる見込みです。

## 今後の展望

今回の実験を通じて、顔認識の実用性、クライアントサイドでの推論の可能性、そして最新モデルによる性能向上が確認できました。今後はこれらの要素を統合し、より高度な画像検索システムの構築を目指していきます。
