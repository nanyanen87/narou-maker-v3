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

```
AskUserQuestion:
世界観の素材となる3つの単語を入力してください
（例: 海 時計 猫）
```

### Step 1.2: 自動実行（Master → Subagent）

ユーザー入力完了後、以下を自動で実行:

#### 【Master層】設計フェーズ（メイン会話で実行）

1. **プロジェクトID生成**
   ```bash
   date +%Y%m%d-%H%M%S
   ```
   → `outputs/<timestamp>_<slug>/` 作成、`input.md` に入力を保存

2. **世界観構築** (`/world-building`)
   - 入力単語を元に `worldview.md` 生成
   - 必要なら `WebSearch` で神話・文化を調査

#### 【確認】世界観承認

```
AskUserQuestion:
世界観が完成しました。

[世界観の要約を表示]

このままプロット構築に進みますか？
1. このまま進む
2. 世界観を修正してから進む
3. ここで一旦終了（後で /start-writing で再開可能）
```

- 2を選んだ場合 → 修正点を聞いて `/world-building` を再実行
- 3を選んだ場合 → 保存済みファイルを案内して終了

3. **プロット＋キャラクター構築** (`/plot-template`)
   - 世界観を元に対話的にプロット方針を決定
   - `plot.md`、`characters.md` 生成
   - 参照: `narou-flavor` スキル

#### 【確認】プロット承認

```
AskUserQuestion:
プロットとキャラクターが完成しました。

[プロットの要約を表示]
[キャラクター一覧を表示]

このまま章の執筆に進みますか？
1. このまま進む
2. プロットを修正してから進む
3. 世界観から見直す
4. ここで一旦終了（後で /start-writing で再開可能）
```

- 2を選んだ場合 → `/plot-revision` で書き直し
- 3を選んだ場合 → `/world-building` から再実行
- 4を選んだ場合 → 保存済みファイルを案内して終了

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
ユーザー: /start-writing

Claude: 何をしますか？
        1. 新規プロジェクト開始
        2. 既存プロジェクトの続き

ユーザー: 1

Claude: 世界観の素材となる3つの単語を入力してください
        （例: 海 時計 猫）

ユーザー: 図書館 幽霊 コーヒー

Claude: 了解。世界観を構築します。
        [世界観構築中...]

        世界観が完成しました。
        [世界観の要約を表示]

        このままプロット構築に進みますか？
        1. このまま進む
        2. 世界観を修正してから進む
        3. ここで一旦終了

ユーザー: 1

Claude: 続いてプロットを構築します。
        [plot-template が対話的に方針を確認]

        プロットとキャラクターが完成しました。
        [プロットの要約を表示]
        [キャラクター一覧を表示]

        このまま章の執筆に進みますか？
        1. このまま進む
        2. プロットを修正してから進む
        3. 世界観から見直す
        4. ここで一旦終了

ユーザー: 1

Claude: 了解。章を執筆します。
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
