# net-vis

**net-vis** は、複雑なネットワーク構造を JupyterLab 上で直感的に可視化・分析するための Python ライブラリです。

NetworkX との親和性が高く、わずか数行のコードでリッチなインタラクティブグラフを描画できます。

[![net-vis on PyPI](https://img.shields.io/pypi/v/net-vis?color=blue)](https://pypi.org/project/net-vis/)

## 主な特徴

*   **Interactive Visualization**: D3.js ベースの滑らかなアニメーション。ズーム、パン、ノードのドラッグ＆ドロップが可能。
*   **Simple API**: `Plotter` クラスに NetworkX のグラフオブジェクトを渡すだけのシンプル設計。
*   **Export to HTML**: インターネット接続不要で動作する、スタンドアロンな HTML ファイルとして出力・共有が可能。
*   **Versatile Layouts**: Force-directed (Spring), Circular, Kamada-Kawai など多様なレイアウトアルゴリズムをサポート。

## インストール

```bash
uv pip install net-vis
# または
pip install net-vis
```

## 使用例

```python
import networkx as nx
from net_vis import Plotter

# グラフデータの作成
G = nx.karate_club_graph()

# 可視化
plotter = Plotter()
plotter.plot(G, title="Karate Club Graph")
```

## リンク

*   **PyPI**: [https://pypi.org/project/net-vis/](https://pypi.org/project/net-vis/)
*   **GitHub**: [https://github.com/cmscom/netvis]
