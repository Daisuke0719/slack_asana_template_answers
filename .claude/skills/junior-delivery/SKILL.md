---
name: junior-delivery
description: ジュニアコンサルタントがAsanaで割り当てられたRFPタスクを実行し、GitHub上に成果物を作成してレビュー依頼する際に使用する。
---
# Junior Delivery
## Goal
Asanaタスクの目的と完了条件に沿って成果物を作成し、セルフレビュー後にレビュー可能な状態へする。
## Workflow
1. Asana MCPで本人担当の未完了タスクを取得する。
2. タスク説明、完了条件、最近のコメント、参考情報を読む。
3. handson-data/rfp/a-bank-rfp.md を読む。
4. 初回ならv1、修正なら次版として成果物を作成する。
5. 完了条件、抜け漏れ、事実根拠、空欄をセルフレビューする。
6. workspace配下の対応するversionディレクトリへ保存する。
7. 成果物名、パス、実施内容、セルフレビュー結果、確認事項を含むAsanaコメント案を作る。
8. ユーザー確認後にレビュー依頼を投稿する。
## Versioning
既存版を上書きしない。v1の次はv2として新規保存する。
## Review request
初回は[REVIEW_REQUEST]、2回目以降は[RE_REVIEW_REQUEST]を使い、成果物パス・版・実施内容・確認点を記載する。
## Safety
タスクを自己承認しない。顧客情報を捏造しない。マネージャー判断を代行しない。
