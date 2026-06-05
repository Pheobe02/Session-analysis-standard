---
name: session-analysis-standard
description: Analyze Chat4Data online user session exports and classify issue sessions. Use when the user provides Chat4Data session CSVs, asks for daily online session analysis, funnel metrics, launch conversion, collection success rate, end-to-end success rate, issue-session classification, typical cases, Feishu-style issue summaries, or wants outputs following the established Chat4Data session analysis standard.
---

# Session Analysis Standard

Use this skill to analyze Chat4Data online session exports with the agreed metric definitions and issue taxonomy.

## Required Reference

Before producing an analysis, read:

- `references/session-analysis-standard.md`

Use that file as the source of truth for metric formulas, internal account filtering, issue categories, attribution rules, output fields, sorting, and typical case treatment.

## Workflow

1. Inspect the input CSV schema and row count. Handle multiline `对话详情` fields with a real CSV parser.
2. Filter internal accounts before computing metrics.
3. Compute the core funnel metrics:
   - session count after filtering
   - launch collection sessions
   - collection-success sessions
   - non-launch sessions
   - launched-but-no-data sessions
   - export-success sessions
   - launch conversion rate
   - launch-to-data success rate
   - end-to-end collection success rate
4. Classify sessions by root cause, not only by the last error message.
5. Keep two lenses:
   - broad issue sessions
   - effective product / Agent issue sessions
6. Sort issue categories by frequency descending. For ties, prioritize effective product / Agent issues, then higher priority.
7. Produce two outputs unless the user asks otherwise:
   - session analysis summary report
   - issue-session detail table

## Classification Guardrails

- Treat `已生成预览数据` as a normal preview/confirmation state by default.
- Classify preview followed by no user response as `生成预览数据后用户未回应`.
- Classify repeated user `Continue` with repeated previews and no progress as `Agent 理解或流程问题`.
- If the final error is credit exhaustion but earlier behavior shows repeated exploration, repeated preview, or stalled flow, classify by the upstream Agent/process issue and add an abnormal credit-exhaustion result tag.
- If an empty final response means the user closed the plugin or manually interrupted, classify as `用户主动中断 / 放弃`.
- If a user-interruption case had earlier loading failures, repeated exploration, user confusion, or long waiting signals, mark it as requiring manual review.
- Split website login/permission limitations from element-location failures:
  - login wall / OAuth / permission prompt -> `目标网站登录 / 权限限制，需用户介入`
  - accessible page but wrong/missing elements -> `目标元素定位失败 / 页面结构识别失败`

## Output Style

Lead with the conclusion. Use concise Chinese business-report style. For every issue category in the summary, include count, share, typical case, user URL, and short reason. Keep categories and typical cases ordered by frequency.
