# ハンズオン当日の流れ

第2回ハンズオン「Slack × Claude Code in Slack × Asana MCP × GitHub」の実施手順です。

## このハンズオンで体験すること

Claude Codeの画面を直接操作せず、**Slackの `@Claude` だけ**で、RFP対応タスク1本のレビューループを最後まで回します。

前提となる考え方は次の通りです。

- 業務状態はClaudeの会話履歴に持たせない。
- 正式なタスク・コメント・担当は **Asana**、成果物とloop-stateは **GitHub**、人間とAIの会話UIは **Slackスレッド** に置く。
- セッションが切れても、AsanaとGitHubを読めば続きから再開できる。

## 役割

参加者1人が **ジュニアコンサルタント役** と **マネージャー役** を切り替えて演じます。
役の切り替えは、Asanaタスクの担当者（Assignee）の付け替えで表現します。

| 役 | やること |
|---|---|
| ジュニア | 成果物の作成・修正、セルフレビュー、レビュー依頼 |
| マネージャー | レビュー実施、指摘、承認、タスク完了 |

## 対象タスク

| 項目 | 値 |
|---|---|
| Asanaプロジェクト | 提案RFP対応 |
| 案件 | A銀行 顧客データ分析・営業高度化基盤構築提案 |
| タスク | RFP原文と要求事項を整理する（TASK-01） |
| 入力資料 | `handson-data/rfp/a-bank-rfp.md` |
| 成果物 | `workspace/task-01-requirements/v1/requirements-v1.md` → `v2/requirements-v2.md` |
| 状態ファイル | `loop-state/tasks/task-01.json` |

---

## Phase 0: 事前準備

1. このリポジトリを自分のGitHubへ作成またはコピーする。
2. Claude Code on the web からこのリポジトリへアクセスできることを確認する。
3. SlackのClaudeアプリで自分のClaude Pro / Maxアカウントを接続する。
4. （任意）Asana MCPをセットアップする。手順とリスクは [README.md](../README.md) の「Asana MCP のセットアップ」を参照。
   - 動作確認: `@Claude このリポジトリで、Asanaのワークスペース一覧を表示して`
   - `asana_list_workspaces` が実行されればOK。
5. Asanaでプロジェクト「**提案RFP対応**」を作成し、`docs/asana_rfp_handson_role_based.csv` を取り込む（3タスク）。
6. **取り込んだタスクの担当者を自分に設定する。**
   CSVの `Assignee` 列は意図的に空にしてあります。任意の文字列を入れるとAsanaのCSVインポートが担当者を解決できず失敗するため、インポート後にAsana上で手動設定してください。

### Phase 0 の完了条件

- Slackから `@Claude` でこのリポジトリを対象にセッションが開始できる。
- `@Claude 自分のAsanaタスクを見せて` で「RFP原文と要求事項を整理する」が返ってくる。

---

## Phase 1〜9: 業務ループ

すべてSlackの同一スレッドから依頼します。依頼文は例であり、自然言語であれば言い回しは自由です。
`rfp-task-router` Skillが意図を分類し、適切なSkillへルーティングします。

### 1. タスク確認（ジュニア）

```
@Claude 自分のAsanaタスクを見せて
```

`asana_get_my_tasks` で未完了タスクを一覧化します。

> **注意**: Asana無料プランでは `asana_search_tasks`（Search API）が402エラーになります。
> タスク取得は `asana_get_my_tasks` または `asana_get_tasks_for_project`、
> プロジェクト検索は `asana_search_projects` を使ってください。

### 2. 成果物作成（ジュニア）

```
@Claude 「RFP原文と要求事項を整理する」を進めて
```

`junior-delivery` Skillが動きます。

- Asanaからタスク名、説明、完了条件、期限、コメントを取得する。
- `handson-data/rfp/a-bank-rfp.md` から要求事項を抽出する。
- `workspace/task-01-requirements/v1/requirements-v1.md` として保存する。

作業計画と成果物の構成が提示されるので、内容を確認してから進めてください。

### 3. セルフレビュー（ジュニア）

タスクの完了条件に対して、`self-review-checklist.md` の観点でセルフレビューします。
2の続きで自動的に実施されますが、明示的に依頼しても構いません。

```
@Claude 完了条件を満たしているかセルフレビューして
```

### 4. レビュー依頼（ジュニア）

```
@Claude レビュー依頼を出して
```

`[REVIEW_REQUEST]` コメント案が提示されます。

- **人間が内容を確認して承認した後にのみ**、Asanaへ投稿されます（Human Gate）。
- 投稿後、担当者をマネージャーへ変更します。
- コメントには絶対パスではなく、プロジェクトルートからの相対パスを記載します。

### 5. マネージャーレビュー（マネージャー）

```
@Claude レビュー依頼が来てないか確認して
```

または対象を明示して:

```
@Claude 「RFP原文と要求事項を整理する」をレビューして
```

`manager-review` Skillが動きます。

- ローカル成果物、タスク完了条件、RFP原文、前回指摘を照合する。
- タスク適合性 → 内容品質 → 成果物間整合性 → 前回指摘への対応状況 の順に評価する。
- 指摘ごとに **重要度 / 対象箇所 / 事実 / 根拠 / 改善案** を記載する。
- `review-verification.md` に従い、根拠のない指摘、過剰な重大度、推測を除外する。

サマリー提示 → マネージャーの明示承認 → `[REVIEW_RESULT]` をAsanaへ投稿 → 担当者をジュニアへ戻す、の順に進みます。

Critical相当の問題、成果物にアクセスできない、根拠箇所を特定できない、AIの確信度が低い、といった場合は自動投稿せず人間の判断を仰ぎます（Human Gate）。

### 6〜7. 修正・v2作成（ジュニア）

```
@Claude レビュー指摘を反映して修正して
```

`revision-handler` Skillが動きます。

- 最新の `[REVIEW_RESULT]` と `loop-state/tasks/task-01.json` を読む。
- **v1を上書きせず**、`workspace/task-01-requirements/v2/requirements-v2.md` を新規作成する。
- 指摘ごとに `resolved` / `partially_resolved` / `unresolved` / `not_applicable` を判定する。
- loop-stateを更新する。

### 8. 再レビュー依頼（ジュニア）

```
@Claude 再レビュー依頼して
```

`[RE_REVIEW_REQUEST]` に、前回Issueごとの対応状況を添えて投稿します。

### 9. 再レビューと承認（マネージャー）

```
@Claude 再レビューして
```

前回指摘が実際に解消しているかを確認します。問題なければ:

```
@Claude 承認してタスクを完了して
```

`[APPROVED]` を投稿し、Asanaタスクを完了にします。

---

## Asanaコメントの状態タグ

Asana MCPにはタグ付与ツールがないため、コメント本文の先頭にテキストでタグを書いて状態を表現します。

```
status: {状態タグ}

{成果物の情報を続けて記載する}
```

| タグ | 意味 | 投稿者 |
|---|---|---|
| `[REVIEW_REQUEST]` | 初回レビュー依頼 | ジュニア |
| `[REVIEW_RESULT]` | レビュー完了・未承認（修正依頼） | マネージャー |
| `[RE_REVIEW_REQUEST]` | 修正版の再レビュー依頼 | ジュニア |
| `[APPROVED]` | 承認 | マネージャー |

コメントの雛形は [asana_comment_templates_ja.md](asana_comment_templates_ja.md) を参照してください。

## loop-state

`loop-state/tasks/task-01.json` が、AsanaとGitHubをまたぐ進行状態を保持します。

```json
{
  "task_id": "TASK-01",
  "task_name": "RFP原文と要求事項を整理する",
  "phase": "junior_working",
  "review_number": 0,
  "current_artifact": null,
  "issues": [],
  "latest_review_request": null
}
```

`phase` は `junior_working` → `waiting_review` → `manager_reviewing` → `revising` → `approved` と遷移します。
各フェーズの後にコミットしておくと、セッションが切れても続きから再開できます。

---

## 演習の仕込み（ファシリテーター向け）

`workspace/task-01-requirements/v1/requirements-v1.md` には、**意図的な不備**が入っています
（ファイル末尾に「演習用に意図的な不備があります」と明記）。
マネージャーレビューでこれらを根拠付きで指摘できるかが見どころです。

| 不備 | 根拠 | v2で解消 |
|---|---|---|
| `DAT-003`（原データ・加工データの7年保存）が欠落 | RFP 3.4 | ○ |
| `SEC-003` の分類が「運用要件」になっている（正しくはセキュリティ要件） | RFP 4.4 はセキュリティ要件の節 | ○ |
| `SEC-004` の回答担当が空欄 | 完了条件「回答担当を記載する」 | ○ |
| 確認事項列が全行空欄 | 完了条件「確認事項を記載する」 | ○（主要行のみ） |
| **重要度の列が存在しない** | 完了条件「重要度を記載する」 | **×（v2でも未解消）** |

最後の1件は **v2でも解消されていません**。再レビューで「未解決」と判定できるかを試す材料として使えます。
すぐに完走させたい場合は、この点をレビュー観点から外して進めてください。

## 押さえるポイント

- 成果物は上書きせず、v1・v2と版を分ける。
- 根拠が確認できないレビュー指摘は断定しない。
- Asanaへの書き込み、承認、タスク完了の前には必ず人間の明示確認を通す（Human Gate）。
- ジュニア役はタスクを完了・承認しない。
- 入力資料にない情報を事実として追加しない。

## つまずいたとき

| 症状 | 確認すること |
|---|---|
| Asanaツールが呼ばれない | README「Asana MCP のセットアップ」の環境変数・Allowed domains。設定は**新しいセッション**から反映されます |
| 402エラー | 無料プランでは Search API が使えません。`asana_get_my_tasks` などに切り替えてください |
| Skillが発動しない | `.claude/skills/` がリポジトリに含まれているか、依頼文が意図を含んでいるか |
| 前回の続きが分からない | Asanaの最新コメントと `loop-state/tasks/task-01.json` を読ませてください。会話履歴には頼りません |
