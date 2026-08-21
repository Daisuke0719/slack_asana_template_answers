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
5. Asanaプロジェクト「提案RFP対応」を作成し、`docs/asana_rfp_handson_role_based.csv` を取り込む
6. 取り込んだタスクの担当者を自分に設定する（CSVの `Assignee` 列は空です）

当日の手順は [docs/HANDSON_FLOW.md](docs/HANDSON_FLOW.md) を確認してください。

## Asana MCP のセットアップ（任意）

> **このセットアップは任意です。** 実施しなくてもハンズオンのGitHub側の流れは体験できます。
> 下記のリスクを読み、納得した方だけ設定してください。

### なぜこの構成なのか

Slack起点のセッションでは claude.ai の Asana コネクタが注入されない既知の不具合があります
（[anthropics/claude-code#82942](https://github.com/anthropics/claude-code/issues/82942)、未修正）。
またAsana公式のMCPサーバー（`mcp.asana.com`）はOAuth専用で、使い捨てVM上で毎回起動する
Slack起点セッションでは認証を維持できません。

そのため、このリポジトリは **Personal Access Token (PAT) で認証できる非公式のMCPサーバー**を
`.mcp.json` に同梱しています。cloud session はリポジトリの `.mcp.json` を自動で読み込みます。

### 事前に理解しておくリスク

- **使用するのはAsana公式ではなく、個人開発のOSSパッケージです**
  （[`@roychri/mcp-server-asana`](https://github.com/roychri/mcp-server-asana)、MIT、
  GitHub 148 stars、月間約14,000ダウンロード）。
  あなたのPATはこの第三者コードに渡されます。
- `npx` はセッション毎にパッケージを取得するため、将来このパッケージが侵害された場合、
  PATが流出する可能性があります。
- **PATはAsanaアカウントの全権限を持ちます。**
- Claude Codeの公式ドキュメントは、cloud environmentの環境変数について
  「専用のシークレットストアではないため、APIキーや認証情報を入れないこと」と明記しています。
  本手順はそれを承知の上での研修用の割り切りです。

**必ず研修専用のAsanaワークスペース／アカウントで実施し、業務用アカウントのPATは使わないでください。
研修終了後は必ずPATを失効させてください。**

### 手順

1. **PATを発行する**
   - <https://app.asana.com/0/my-apps> → 「+ 新しいアクセストークン」
   - 研修用アカウントで発行し、表示されたトークンをコピーしておく（再表示できません）
2. **cloud environment を設定する**
   - <https://claude.ai/code> を開き、入力欄付近の環境セレクタ（雲のアイコン）から
     **Default** 環境の設定（歯車アイコン）を開く
   - **Environment variables** に `.env` 形式で1行追加する
     ```
     ASANA_ACCESS_TOKEN=1/1234567890abcdef...
     ```
   - **Network access** を **Custom** に変更し、**Allowed domains** に次を追加
     ```
     app.asana.com
     ```
   - **「Also include default list of common package managers」に必ずチェックを入れる**
     （外すと `npx` がnpmに到達できず、MCPサーバーが起動しません）
   - 保存する
3. **動作確認**
   - 設定は**保存後に開始したセッションから**反映されます（実行中のセッションには反映されません）
   - Slackのチャンネルで
     `@Claude このリポジトリで、Asanaのワークスペース一覧を表示して` と依頼し、
     `asana_list_workspaces` が実行されることを確認する

### うまくいかないとき

| 症状 | 確認すること |
|---|---|
| トークンがない旨のエラー | 手順2の環境変数名が `ASANA_ACCESS_TOKEN` になっているか |
| MCPサーバーが起動しない | 「Also include default list of common package managers」のチェック |
| Asanaへの接続エラー | Allowed domains に `app.asana.com` があるか |
| 402 エラー | 無料プランではSearch APIが使えません（`CLAUDE.md` 参照） |
| 設定したのに変わらない | 新しいセッションを開始し直す |
