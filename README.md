# RFP Loop Engineering Handson v2

このリポジトリは、第2回ハンズオン「Slack × Claude Code in Slack × Asana MCP × GitHub」で使用します。

## ゴール

Claude Codeの画面を直接操作せず、Slack上の `@Claude` から以下の業務ループを回します。

1. Asanaから担当タスクを取得
2. 成果物をGitHub上に作成
3. セルフレビュー
4. Asanaへレビュー依頼
5. マネージャーAIレビュー
6. 修正依頼
7. v2作成
8. 再レビュー
9. 承認・完了

## 重要な考え方

Claudeの会話履歴を業務状態として使いません。共有状態は次に置きます。

- Asana: 正式なタスク・コメント・担当
- GitHub: 成果物・Skills・loop-state
- Slack thread: 人間とAIの会話UI

## Repository structure

```text
.claude/skills/
  rfp-task-router/
  junior-delivery/
  manager-review/
  revision-handler/
handson-data/rfp/
workspace/
loop-state/
docs/
```

## ハンズオン開始前

1. このリポジトリを自分のGitHubへ作成またはコピーする
2. Claude Code on the webからこのリポジトリへアクセスできることを確認する
3. SlackのClaudeアプリで自分のClaude Pro / Maxアカウントを接続する
4. Asana MCPを利用できる状態にする
5. `.claude/skills/` の TODO を埋める

詳細は `docs/HANDSON_FLOW.md` を確認してください。
