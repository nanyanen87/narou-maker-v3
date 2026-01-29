---
name: refinement
description: 執筆済みの章を推敲・検証する。
context: fork
agent: general-purpose
---

# 推敲・検証

執筆済みの章を推敲し、品質を向上させよ。

## 入力

- プロジェクトID: $ARGUMENTS[0]
- 対象章: $ARGUMENTS[1]（章番号 or "all"）
- `outputs/<project-id>/chapters/XX.md`（対象章）
- `outputs/<project-id>/worldview.md`（整合性チェック用）
- `outputs/<project-id>/characters.md`（整合性チェック用）

## チェック項目

### 1. 文章品質
- [ ] 誤字脱字
- [ ] 文法エラー
- [ ] 冗長な表現
- [ ] 読みにくい長文

### 2. 整合性
- [ ] 世界観設定との矛盾
- [ ] キャラクターの性格・口調のブレ
- [ ] 時系列の矛盾
- [ ] 前章との連続性

### 3. エンタメ性
- [ ] テンポの悪い箇所
- [ ] 盛り上がりの欠如
- [ ] 章末の引きの弱さ

## 出力

修正後の本文で `outputs/<project-id>/chapters/XX.md` を上書き。

また、`outputs/<project-id>/refinement-log.md` に記録:

```markdown
## 第X章 推敲ログ

### 修正箇所
1. 〇〇を△△に修正（理由: ××）
2. ...

### 残課題
- （あれば）
```

## 注意点

- 作者の文体を尊重（過度な改変をしない）
- 修正理由を明確に記録
- 大きな構成変更が必要な場合は提案に留める
