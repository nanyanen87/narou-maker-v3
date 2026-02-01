---
name: start-writing
description: 小説生成ワークフローを開始または再開する
---

# 小説生成ワークフロー v2

3段階構成。各段階で修正確認ループを回す。

---

## Phase 0: モード選択

```
AskUserQuestion:
何をしますか？
1. 新規プロジェクト開始
2. 既存プロジェクトの続き
```

---

## Phase 1: 世界観構築

### Step 1.1: 入力収集（新規の場合）

```
AskUserQuestion:
世界観の素材となる3つの単語を入力してください
（例: 海 時計 猫）
```

### Step 1.2: 世界観構築

1. **プロジェクトID生成**
   ```bash
   date +%Y%m%d-%H%M%S
   ```
   → `outputs/<timestamp>_<slug>/` 作成、`input.md` に入力を保存

2. **世界観構築** (`/world-building`)
   - 入力単語を元に `worldview.md` 生成

### Step 1.3: 修正確認ループ

```
AskUserQuestion:
世界観が完成しました。

[世界観の要約を表示]

1. このまま進む → Phase 2へ
2. 修正してから進む → 修正点を聞いて再構築
3. 一旦終了 → 保存済みファイルを案内
```

- 「修正」選択時 → 修正指示を聞いて `/world-building` を再実行
- 承認されるまでループ

---

## Phase 2: 脚本構築

### Step 2.1: プロット＋キャラクター構築

**プロット構築** (`/plot-template`)
- 世界観を元に `plot.md`、`characters.md` 生成
- 参照: `narou-flavor` スキル

### Step 2.2: 修正確認ループ

```
AskUserQuestion:
プロットとキャラクターが完成しました。

[プロットの要約を表示]
[キャラクター一覧を表示]

1. このまま進む → Phase 3へ
2. プロットを修正してから進む → `/plot-revision` で書き直し
3. 世界観から見直す → Phase 1に戻る
4. 一旦終了 → 保存済みファイルを案内
```

- 「修正」選択時 → `/plot-revision` を実行して再度確認
- 承認されるまでループ

---

## Phase 3: 執筆

### Step 3.1: 章執筆

**章執筆** (`/chapter-writing`)
- Task tool で全章を並列実行（subagent_type: general-purpose）
- `outputs/<project-id>/chapters/` に出力

**推敲** (`/refinement`)
- 各章を推敲・検証

### Step 3.2: 修正確認ループ

```
AskUserQuestion:
全章の執筆と推敲が完了しました。

[各章の要約を表示]
[総文字数を表示]

1. 完成 → 完了報告
2. 特定の章を書き直す → 章番号を聞いて再執筆
3. プロットから見直す → Phase 2に戻る
4. 世界観から見直す → Phase 1に戻る
```

- 「書き直し」選択時 → 該当章の `/chapter-writing` を再実行
- 承認されるまでループ

---

## Phase 4: 完了

- 生成されたファイル一覧を表示
- 総文字数を報告
- `final.md` への結合（オプション）

---

## 既存プロジェクト再開（Phase 0で「続き」選択時）

### 進捗確認

`outputs/` を走査してプロジェクト一覧を取得し表示:

```
AskUserQuestion:
どのプロジェクトを再開しますか？
1. 20260129-211701_quake-mixer
2. 20260128-153022_forest-clock
3. ...
```

### 再開ポイント選択

選択されたプロジェクトのファイルを確認し、進捗を表示:

```
AskUserQuestion:
現在の進捗:
✓ worldview.md
✓ plot.md
✓ characters.md
✗ chapters/

どこから再開しますか？
1. 次のフェーズから続行
2. 世界観から見直す
3. プロットから見直す
```

→ 該当Phaseの修正確認ループから再開

---

## エラー処理

- プロジェクトが存在しない → 新規作成を促す
- 世界観がない状態で続きを選んだ → Phase 1から開始
- WebSearchが失敗 → 手持ちの知識で続行
