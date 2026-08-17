# HANDSON FLOW

## Phase 1: Skill構築
参加者は `.claude/skills/` の4つのSkillを穴埋めします。

1. `rfp-task-router`
2. `junior-delivery`
3. `manager-review`
4. `revision-handler`

## Phase 2: Junior
Slackで `@Claude` を使い、Asanaから自分のタスクを確認します。

例:

```text
@Claude
これはRFPハンズオンのClaude Codeタスクです。
このリポジトリを使い、Asanaの「提案RFP対応」から自分の担当タスクを確認してください。
```

続けて成果物v1を作成し、セルフレビュー後にAsanaへレビュー依頼を送ります。

## Phase 3: Manager
マネージャー役はSlackからレビュー依頼を確認し、成果物v1とRFP原文を比較してレビューします。

## Phase 4: Revision
ジュニア役に戻り、Asanaのレビューコメントと `loop-state` を参照してv2を作成します。

## Phase 5: Re-review
マネージャー役が前回Issueの解消状況と新規問題を確認し、承認します。
