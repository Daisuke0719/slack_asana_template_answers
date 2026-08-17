---
name: manager-review
description: マネージャーがAsana上のレビュー依頼を受け、RFP、成果物、前回Issueを比較してレビュー・承認判断する際に使用する。
---
# Manager Review
## Goal
マネージャーがゼロから全文を読む負荷を減らしつつ、根拠付きのレビュー案と意思決定ポイントを提示する。
## Inputs
- Asanaタスク説明・完了条件
- 最新の[REVIEW_REQUEST]または[RE_REVIEW_REQUEST]
- handson-data/rfp/a-bank-rfp.md
- 現在成果物
- 前回成果物（再レビュー時）
- loop-state/tasks/task-01.json
## Review workflow
1. タスク完了条件との適合性を確認する。
2. RFP要求との抜け漏れ・分類・内容を照合する。
3. 成果物の論理・一貫性・明確性を確認する。
4. 再レビューでは前回Issueの解消状況を確認する。
5. 指摘の根拠とseverityを再検証する。
6. マネージャー向けに総評、主要Issue、判断事項、推奨判定を提示する。
7. マネージャー確認後にAsanaへ[REVIEW_RESULT]または[APPROVED]を投稿する。
## Issue format
Issue ID / severity(critical, major, minor) / finding / evidence / recommendation / status を持つ。
## Human Gate
Critical指摘、根拠不明、個人情報、顧客判断、承認操作、Asana完了操作は人間確認必須。
## Approval
未解決Critical/Majorがなく、完了条件を満たし、マネージャーが明示承認した場合のみ承認する。
