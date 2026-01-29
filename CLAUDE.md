# 簡易小説生成工場

短編小説を生成するワークフロー実験プロジェクト。

## 構成

- `docs/` - ワークフロー設計ドキュメント
- `.claude/skills/` - 各工程のスキル定義
- `outputs/` - 生成物の出力先

## ワークフロー

1. プロジェクト開始（project-id生成: `YYYYMMDD-HHMMSS_slug`）
2. `/world-building <project-id> <keywords>` → 世界観構築
3. `/plot-template <project-id>` → プロット作成（narou-flavor参照）
4. `/chapter-writing <project-id> N` → 第N章執筆（subagent）
5. `/refinement <project-id> N` → 第N章推敲（subagent）

## 出力先

```
outputs/<project-id>/
  input.md          - 入力キーワード・指示
  worldview.md      - 世界観設定
  characters.md     - キャラクター設定
  plot.md           - プロット骨子
  chapters/
    01.md           - 各章本文
    02.md
    ...
  final.md          - 最終成果物
```

---

## 人間との対話ルール

スキル実行時に判断が必要な場合は、必ず `AskUserQuestion` を使用して人間に確認する。

### 基本原則

- **選択肢は番号付きで提示する**
- **自分で判断できる場合は進めてよい。迷ったら聞く**
- **複数の質問がある場合は一度にまとめて聞く**

### AskUserQuestionを使う場面

| スキル | 確認事項 |
|:--|:--|
| world-building | 単語の解釈、世界観の方向性、活用タイプ |
| plot-template | テンプレート選択、ビート数、結末のトーン |
| chapter-writing | 文体の確認、シーンの方向性 |
| refinement | 大きな修正の承認 |

### 選択肢の形式

```
○○を選んでください:
1. 選択肢A（説明）
2. 選択肢B（説明）
3. 選択肢C（説明）
4. その他（自由入力）
```
