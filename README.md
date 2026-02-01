# 短編小説生成ワークフロー

3つのキーワードから短編小説を自動生成するClaude Code実験プロジェクト。

## 必要なもの

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)

## 使い方

```bash
# プロジェクトに移動
cd narou-maker-v3

# Claude Codeを起動
claude

# ワークフローを開始
/start-writing
```

あとは対話形式で進行します。

## ワークフロー

```
3つのキーワード入力
      ↓
Phase 1: 世界観構築
      ↓
Phase 2: プロット＋キャラクター作成
      ↓
Phase 3: 章執筆＋推敲
      ↓
完成原稿
```

各フェーズで確認が入るので、修正や方向転換が可能です。

## 成果物

`outputs/<project-id>/` に以下が出力されます:

- `worldview.md` - 世界観設定
- `plot.md` - プロット骨子
- `characters.md` - キャラクター設定
- `chapters/` - 各章本文
- `final.md` - 最終成果物

## ライセンス

MIT
