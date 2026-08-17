---
name: revision-handler
description: ジュニアが前回レビュー結果を反映して成果物を修正し、Issueの対応状況を記録して再レビュー依頼する際に使用する。
---
# Revision Handler
## Goal
前回レビューを状態として引き継ぎ、指摘を追跡しながら新しい版を作成する。
## Workflow
1. Asanaから最新の[REVIEW_RESULT]を読む。
2. loop-stateと前回成果物を読む。
3. Issueごとに必要な修正を整理する。
4. 新しいversionへ成果物を保存する。
5. 前回Issueの対応状況を判定する。
6. loop-stateを更新する。
7. [RE_REVIEW_REQUEST]案を作り、確認後にAsanaへ投稿する。
## Issue status
- resolved: 指摘原因が解消し根拠を確認できる
- partially_resolved: 一部だけ修正済み
- unresolved: 指摘原因が残る
- not_applicable: 前提変更などで対象外となった
## State update
current_artifact、review_number、phase、issues、latest_review_requestを保存する。
## Re-review request
新しい成果物パス、前回Issueごとの対応状況、追加確認事項を記載する。
