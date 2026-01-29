---
name: start-writing
description: 小説生成ワークフローを開始または再開する
---

# 小説生成ワークフロー

ユーザーの操作は `AskUserQuestion` への回答のみ。それ以外は自動実行。

---

## Phase 0: モード選択

```
AskUserQuestion:
何をしますか？
1. 新規プロジェクト開始
2. 既存プロジェクトの続き
```

---

## Phase 1: 新規プロジェクト（1を選んだ場合）

### Step 1.1: 入力収集

以下を一度に聞く:

```
AskUserQuestion（複数質問）:

Q1: 世界観の素材となる3つの単語を入力してください
    （例: 海 時計 猫）

Q2: 世界観のトーンは？
    1. ライト（明るい、冒険）
    2. ダーク（シリアス、重厚）
    3. バランス（明暗混在）

Q3: 面白さの核は何にしますか？
    1. 意外性（読者の予想を裏切るオチ）
    2. 切り口（普通と違う視点・解釈）
    3. 凝縮（一瞬に人生を詰め込む）
    4. 余韻（語らないことで語る）
```

### Step 1.2: 核の具体化

```
AskUserQuestion:
「{選んだ核}」を具体的にどう実現しますか？

例（意外性の場合）:
1. 助けに来た仲間が実は黒幕
2. 救おうとした相手が救いを拒否
3. 目的達成が最悪の結果を招く
4. その他（自由入力）
```

### Step 1.3: 構造選択

```
AskUserQuestion:
物語の構造は？（核に合うものを推奨）
1. シンデレラ型（不遇→逆転）
2. 行きて帰りし（冒険→成長）
3. 悲劇型（上昇→転落）
4. 再生型（囚われ→解放）

ビート数は？
1. 3ビート（短編: 〜10,000字）
2. 5ビート（中編: 〜30,000字）

結末は？
1. ハッピーエンド
2. ビターエンド
3. 余韻を残す
```

### Step 1.4: 自動実行（Master → Subagent）

ユーザー入力完了後、以下を自動で実行:

#### 【Master層】設計フェーズ（メイン会話で実行）

1. **プロジェクトID生成**
   ```bash
   date +%Y%m%d-%H%M%S
   ```
   → `outputs/<timestamp>_<slug>/` 作成、`input.md` に入力を保存

2. **世界観構築** (`/world-building`)
   - 入力単語と回答を元に `worldview.md` 生成
   - 必要なら `WebSearch` で神話・文化を調査

3. **プロット＋キャラクター構築** (`/plot-template`)
   - 面白さの核 + 構造 + 結末を元に `plot.md` 生成
   - `characters.md` 生成
   - 参照: `narou-flavor` スキル

#### 【Subagent層】実装フェーズ（context: fork で実行）

4. **章執筆** (`/chapter-writing`)
   - Task tool で全章を並列実行（subagent_type: general-purpose）
   - `outputs/<project-id>/chapters/` に出力

5. **推敲**（オプション、`/refinement`）
   - 必要に応じて各章を推敲

#### 【完了】

6. **完了報告**
   - 生成されたファイル一覧を表示
   - 総文字数を報告

---

## Phase 2: 既存プロジェクト続き（2を選んだ場合）

### Step 2.1: プロジェクト選択

`outputs/` を走査してプロジェクト一覧を取得し表示:

```
AskUserQuestion:
どのプロジェクトを再開しますか？
1. 20260129-211701_quake-mixer
2. 20260128-153022_forest-clock
3. ...
```

### Step 2.2: 進捗確認と再開ポイント選択

選択されたプロジェクトのファイルを確認し、進捗を表示:

```
AskUserQuestion:
現在の進捗:
✓ worldview.md
✓ plot.md
✓ characters.md
✓ chapters/01.md
✓ chapters/02.md
✗ chapters/03.md
✗ chapters/04.md
✗ chapters/05.md

どこから再開しますか？
1. 未執筆の章（03〜05）を執筆
2. 全章を書き直し
3. プロットからやり直し
4. 世界観からやり直し
```

### Step 2.3: 自動実行

選択に応じて該当スキルを実行:

| 選択 | 実行内容 | 実行層 |
|:--|:--|:--|
| 未執筆の章を執筆 | `/chapter-writing` を並列実行 | Subagent |
| 全章を書き直し | `/chapter-writing` で全章再生成 | Subagent |
| プロットからやり直し | `/plot-template` → `/chapter-writing` | Master → Subagent |
| 世界観からやり直し | `/world-building` → `/plot-template` → `/chapter-writing` | Master → Subagent |

---

## エラー処理

- プロジェクトが存在しない → 新規作成を促す
- 世界観がない状態で続きを選んだ → world-buildingから開始
- WebSearchが失敗 → 手持ちの知識で続行

---

## 実行例

```
ユーザー: /start

Claude: 何をしますか？
        1. 新規プロジェクト開始
        2. 既存プロジェクトの続き

ユーザー: 1

Claude: [3つの質問を一度に表示]

ユーザー: [回答]

Claude: 核の具体化について...

ユーザー: [回答]

Claude: 構造・ビート数・結末について...

ユーザー: [回答]

Claude: 了解。自動生成を開始します。
        [世界観構築中...]
        [プロット構築中...]
        [第1章執筆中...]
        [第2章執筆中...]
        [第3章執筆中...]

        完了しました。
        - worldview.md (2,500字)
        - plot.md (800字)
        - characters.md (600字)
        - chapters/01.md (3,200字)
        - chapters/02.md (3,500字)
        - chapters/03.md (3,100字)
        総文字数: 約13,700字
```
