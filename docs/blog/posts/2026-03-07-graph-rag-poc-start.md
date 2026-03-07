---
date: 2026-03-07
authors:
  - terapyon
categories:
  - Projects
tags:
  - AI
  - RAG
  - GraphRAG
  - Neo4j
  - Python
slug: graph-rag-poc-start
title: Graph RAG と Temporal Knowledge Graph の実験：点から線へ
---

これまでの実験では、テキストや画像の「ベクトル検索」によって、類似情報を高速に見つける技術を検証してきました。
しかし、ベクトル検索だけでは「AとBの関係は？」「前回の会議から何が変わった？」といった、構造や時間を伴う質問には弱いという課題がありました。

そこで新たに **Graph RAG (Retrieval-Augmented Generation)** の実験プロジェクトを立ち上げ、特に「時系列情報（Time）」をグラフに組み込むアプローチを検証しました。

<!-- more -->

## Graph RAG とは？

Graph RAG は、テキストデータをナレッジグラフ（ノードとエッジのネットワーク）に変換し、LLMがその構造を利用して回答を生成する手法です。

[Graph RAG PoC プロジェクト](../../projects/graph-rag-poc.md) では、以下の技術スタックを用いて、ローカル環境（Local LLM）で完結するシステムを構築しました。

*   **Database**: Neo4j 5
*   **LLM**: Ollama (Qwen2.5 / Qwen3.5)
*   **Graph Engine**: LightRAGベースの独自拡張

## ハイライト：Temporal Knowledge Graph

特に興味深い成果が出たのが、PyCon JPの運営会議議事録を用いた実験（Series 4）です。

通常のRAGでは、古い議事録と新しい議事録が単に「類似文書」としてヒットしてしまい、最新の決定事項が埋もれてしまうことがあります。
今回構築した **Temporal Knowledge Graph** では、会議（Meeting）ノード同士を `NEXT_MEETING` というエッジで時系列順に接続し、さらに各トピックの議論が継続しているかを追跡できるようにしました。

これにより、LLMはグラフを辿って「この件は第X回会議で保留され、第Y回会議で決定された」という経緯を正確に説明できるようになりました。

## 結論と今後の課題

ローカルLLMもかなり良くなっていますが、Graphを生成するという部分では、まだ容易に使えるものには見えないというのが正直な感想です。

*   **ローカルLLMの実力**:
    Ollama (特にQwen系モデル) は非常に優秀で、推論速度・品質ともにローカル環境での実用に十分耐えうると感じました。

*   **グラフ生成の壁**:
    一方で、非構造化テキストからナレッジグラフを自動生成する部分（Entity Extraction, Relation Extraction）については、まだ「容易に使える」レベルには達していない印象です。単純な抽出ではノイズが多く、ドメイン特化の調整が必要でした。

これがローカルLLMやマシンスペックの限界なのか、GraphRAGという手法自体がまだ発展途上によるものなのかは現時点では断定できませんが、今後の技術進化を待ちつつ、実験を続けていきたいと思います。

*   [プロジェクト詳細](../../projects/graph-rag-poc.md)
*   [GitHub: cmscom/graph-rag-poc](https://github.com/cmscom/graph-rag-poc)
