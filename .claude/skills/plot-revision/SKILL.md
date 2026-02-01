---
name: plot-revision
description: 既存のプロットを診断し、素材を活かして根本から書き直す。
---

# プロット書き直し

面白くないプロットを、面白くする。

---

## 原則

**修正ではなく、書き直し。**

- 素材（キーワード、トーン）は活かす
- 構造は一から作り直す
- 安全な案は出さない

---

## 手順

### 0. バージョン保存

**書き直す前に、現在のファイルをバックアップする。**

```bash
# versions/ フォルダがなければ作成
mkdir -p outputs/<project-id>/versions

# 次のバージョン番号を取得
# plot_v1.md, plot_v2.md, ... と連番で保存
ls outputs/<project-id>/versions/plot_v*.md 2>/dev/null | wc -l
# → 結果+1 が次のバージョン番号

# 現在のファイルをバックアップ
cp outputs/<project-id>/plot.md outputs/<project-id>/versions/plot_v<N>.md

# worldview.md も変更する場合は同様に
cp outputs/<project-id>/worldview.md outputs/<project-id>/versions/worldview_v<N>.md
```

### 1. 診断

現在のプロットの問題点を特定する。

参照: `narou-flavor`（なろう的面白さモデル）

### 2. 切り口の提案

素材を活かしつつ、根本的に違う切り口を5つ提案する。

極端に振る。

### 3. 選択

`AskUserQuestion` で切り口を選ばせる。

### 4. 再構築

選んだ切り口でプロットを再生成する。

必要に応じて `worldview.md`、`characters.md` も更新。

---

## 入力

- プロジェクトID: $ARGUMENTS
- `outputs/<project-id>/plot.md`

## 出力

- `outputs/<project-id>/versions/plot_v<N>.md`（バックアップ）
- `outputs/<project-id>/plot.md`（書き直し後）
