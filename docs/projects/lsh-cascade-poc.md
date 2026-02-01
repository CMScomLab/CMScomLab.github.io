# LSH Cascade PoC

**ITQ-LSH** (Iterative Quantization Locality Sensitive Hashing) と独自のカスケードフィルタリング手法を組み合わせた、高速かつメモリ効率の高いベクトル検索エンジンの概念実証（PoC）プロジェクトです。

## プロジェクトの目的

大規模言語モデル（LLM）やRAG（Retrieval-Augmented Generation）の普及に伴い、ベクトル検索の重要性が高まっています。しかし、数百万〜数億規模のデータセットに対する検索は、計算コストとメモリ消費が課題となります。

本プロジェクトでは、検索精度を維持しつつ、計算リソースを最小限に抑えるための新しいアーキテクチャ「LSH Cascade」を提案・検証しました。

## アーキテクチャ

検索プロセスを以下の3段階に分割することで、候補を効率的に絞り込みます。

1.  **Overlap Segment Filter**: 
    128bitハッシュを短いセグメントに分割し、一部が一致する候補を高速に抽出。検索対象を約 **87.5% 削減** します。
2.  **Hamming Distance Filter**:
    絞り込まれた候補に対して、バイナリ演算で高速なハミング距離計算を行い、さらに選抜します。
3.  **Cosine Similarity Ranking**:
    最終的な少数精鋭の候補に対してのみ、高精度なコサイン類似度計算を行います。

## 実験結果

Wikipedia 記事 40万件を用いた実験において、以下の成果を確認しました。

*   **高速化**: 従来の HNSW (DuckDB) と比較して **約 40% の検索速度向上**。
*   **精度維持**: Recall@10 で **83.7%** の再現率を達成。実用的な精度を維持しています。
*   **最適構成**: 埋め込みモデル `multilingual-e5-base` (768次元)、ハッシュ長 128bit、ウィンドウスライド幅 4bit の設定が最適解の一つであることを示しました。

![実験結果のグラフ](../assets/images/lsh-cascade-result.png)

## リンク

*   **GitHub Repository**: [cmscom/lsh-cascade-poc](https://github.com/cmscom/lsh-cascade-poc)
*   **Final Report (Notebook)**: [00_final_report.ipynb](https://github.com/cmscom/lsh-cascade-poc/blob/main/notebooks/00_final_report.ipynb)
