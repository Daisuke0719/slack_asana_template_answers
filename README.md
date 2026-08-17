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
4. Asana MCPを利用できる状態にする（下記「Asana MCP のセットアップ」）
5. `.claude/skills/` の TODO を埋める

詳細は `docs/HANDSON_FLOW.md` を確認してください。

## Asana MCP のセットアップ

Slack起点のセッションでは claude.ai の Asana コネクタが注入されない既知の不具合があるため
（[anthropics/claude-code#82942](https://github.com/anthropics/claude-code/issues/82942)）、
このリポジトリは Personal Access Token (PAT) で認証する Asana MCP サーバーを
`.mcp.json` に同梱しています。cloud session はリポジトリの `.mcp.json` を自動で読み込むので、
以下の2点を各自の claude.ai 側で設定すれば Slack 起点でも Asana MCP が使えます。

1. **PATを発行する**
   - <https://app.asana.com/0/my-apps> → 「+ 新しいアクセストークン」
   - 研修用ワークスペースのアカウントで発行してください
2. **cloud environment を設定する**
   - <https://claude.ai/code> を開き、入力欄付近の環境セレクタから **Default** 環境の設定を開く
   - **Environment variables** に `ASANA_ACCESS_TOKEN` = 発行したPAT を追加
   - **Network access** を **Custom** にし、デフォルトドメインを含めた上で
     許可ドメインに `app.asana.com` を追加
3. **動作確認**
   - Slackのチャンネルで `@Claude このリポジトリで、Asanaのワークスペース一覧を表示して` と依頼し、
     `asana_list_workspaces` が実行されることを確認する

> **注意**: 環境変数はシークレットストアではありません。PATは研修用ワークスペース専用とし、
> 研修終了後に <https://app.asana.com/0/my-apps> から必ず失効させてください。
