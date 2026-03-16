---
date: 2026-03-16
authors:
  - terapyon
categories:
  - Projects
tags:
  - Vector Search
  - Embedding
  - Voronoi
  - Plone
slug: voronoi-text-embedding
title: Voronoi分割によるテキストEmbeddingの事前枝刈り実験とPlone実装への展開
---

# Voronoi分割によるテキストEmbeddingの事前枝刈り実験とPlone実装への展開

[2026-02-14の記事](2026-02-14-plone-vector-search-released.md) では、`collective.vectorsearch` の初期リリースと、LSH Cascade PoC から取り込んだ近似検索の仕組みを紹介しました。
その後、[2026-03-14の記事](2026-03-14-firestore-voronoi-partitioning.md) で、顔画像の embedding に対して Voronoi 分割による事前枝刈りが有効であることを確認しています。

今回はこの流れを受けて、同じアプローチを **テキスト embedding** に適用できるかを検証しました。
結論から言うと、テキスト側でも Voronoi 分割を前段フィルタとして使える見込みが見えてきました。

<!-- more -->

## テキストEmbeddingでもVoronoi分割は効くのか

画像ベクトルで有効だった手法が、そのままテキスト embedding にも通用するとは限りません。
embedding モデルごとにベクトル空間の分布特性が異なるため、事前に Voronoi 領域で候補を絞り込んでも、検索品質が大きく落ちる可能性があるからです。

そこで今回は、テキスト向け embedding を対象に、Voronoi 分割で候補集合を先に絞ったうえで検索性能を評価しました。
検証の詳細は以下の Notebook にまとめています。

- [111_voronoi_partition_evaluation.ipynb](https://github.com/cmscom/lsh-cascade-poc/blob/main/notebooks/111_voronoi_partition_evaluation.ipynb)
- [112_voronoi_generalization_private.ipynb](https://github.com/cmscom/lsh-cascade-poc/blob/main/notebooks/112_voronoi_generalization_private.ipynb)

今回の結果から、少なくとも現在試しているテキスト embedding では、Voronoi 分割による事前枝刈りが一定の効果を持つことを確認できました。
画像だけに閉じたテクニックではなく、ベクトル検索の前処理としてより汎用的に使える可能性があります。

## Plone実装への展開

この成果は、実験ノートブックだけで終わらせず、Plone 向けベクトル検索実装である `collective.vectorsearch` にも展開を進めています。
現在は Voronoi ベースの絞り込みを組み込み、実データでの挙動確認を進めている段階です。

実装の変更点は以下に記録しています。

- [collective.vectorsearch/CHANGES.rst](https://github.com/collective/collective.vectorsearch/blob/feature/voronoi-algorithm/CHANGES.rst)

LSH 系の事前枝刈りに加えて、Voronoi 分割という別の前段フィルタを持てるようになると、データの性質や運用条件に応じて手法を選びやすくなります。
特に、モデルやデータセットによっては Voronoi の方が素直に効くケースもありそうです。

## 今後の課題

現時点での手応えは良好ですが、最終的な判断には **実データでの検証** が欠かせません。
実験用データではうまく機能しても、実運用の文書集合では分布の偏りや更新頻度の影響を受ける可能性があります。

今後は、LSH Cascade PoC と `collective.vectorsearch` の両方で評価を続けながら、テキスト embedding に対する Voronoi 分割の有効性と適用条件を整理していく予定です。
