---
name: rfp-task-router
description: Slack上の自然言語によるRFP業務依頼を分類し、junior-delivery、manager-review、revision-handlerへルーティングする。
---
# RFP Task Router
## Purpose
Slackを業務UIとして使い、ユーザーの短い依頼から適切なRFP業務処理を選択する。
## Supported intents
- LIST_MY_TASKS: 自分のタスクを確認
- START_DELIVERY: タスクを進める／成果物を作る
- REQUEST_REVIEW: レビュー依頼を送る
- MANAGER_REVIEW: レビューを実施する
- REVISE_ARTIFACT: 指摘を反映して修正する
- APPROVE: 承認してタスクを完了する
- UNKNOWN: 意図が確定できない
## Routing rules
- START_DELIVERY / REQUEST_REVIEW → junior-delivery
- MANAGER_REVIEW → manager-review
- REVISE_ARTIFACT → revision-handler
- LIST_MY_TASKS → Asana MCPで本人担当の未完了タスクを検索
- APPROVE → manager-reviewの承認条件を確認後に実行
## Safety
承認、タスク完了、Asana書き込みは対象タスクとユーザー意図を明示確認する。UNKNOWNは推測で実行しない。
