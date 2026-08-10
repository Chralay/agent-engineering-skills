---
name: review-comment-handler
description: Handle batches of review comments, file comments, PR feedback, Markdown comments, or solution-draft feedback without omissions. Use when Codex must first triage many comments, show which comments share the same logic, decide what can be accepted, rejected, discussed, or verified later, avoid creating premature second-version drafts, then produce a complete final handling checklist and changed-file summary.
---

# Review Comment Handler

## Goal

Process batches of comments with traceability. Keep one row per comment, show when several comments belong to the same logic, and avoid changing files or generating a new proposal version before required discussion is complete.

## Core Rules

- Count the incoming comments before handling them.
- Preserve user-provided numeric ids when present. Otherwise use bare Arabic numerals such as `1`, `2`, `3`. Do not add prefixes such as `Comment`, `评论`, `C`, or `L`.
- Keep exactly one checklist row per input comment. Do not merge comments into one row, even when they share the same logic.
- Use natural-language logic labels in `相关逻辑`, such as `参数来源和提交逻辑` or `按钮禁用和 loading 状态`.
- Use `关联说明` to explain how a comment relates to other comments in the same logic, such as `同一逻辑补充`, `重复提醒`, `修正前一条理解`, `与 4 存在冲突`, or `同一改动可以覆盖`.
- Do not expose long private reasoning. Provide the structured classification, reasons, open questions, and verification requirements the user needs to audit the result.

## Two-Phase Proposal Workflow

Use this workflow when the user provides a first proposal, plan, Markdown draft, or design document plus many comments.

1. First produce only the pre-discussion triage table.
2. Do not edit the proposal body or generate a second version while any comment is marked `需要讨论`, unless the user explicitly asks for a temporary draft.
3. Discuss the unresolved items with the user.
4. After decisions are settled, generate the unified second version or apply file edits once.
5. Then produce the final handling checklist and changed-file summary.

This preserves the comparison as `方案一 -> 讨论 -> 方案二`, instead of producing an intermediate partial `方案二` that later becomes `方案三`.

## Pre-Discussion Triage

Use these initial judgments in the first table:

- `可确定采纳`: The comment should enter the later edit or second version.
- `可确定不采纳`: The comment should not be applied. Always give the reason.
- `需要讨论`: A user decision or clarification is required. State the exact question to discuss.
- `需要后续验证`: The point cannot be proven until implementation, integration, runtime testing, or rollout. State what must be verified, when, and what counts as passing.

Pre-discussion table format:

| 评论编号 | 相关逻辑 | 关联说明 | 评论要点 | 初步判断 | 判断理由/所需信息 | 建议处理 | 涉及文件 |
| --- | --- | --- | --- | --- | --- | --- | --- |

Column rules:

- `涉及文件` in this table means related or expected files, not already changed files. Use file paths named by the comment, files likely to change, or files that must be checked. Use `待确认` when unknown and `无` when no file is involved.
- `判断理由/所需信息` must not be vague. For rejection, explain why. For discussion, state the concrete question. For later verification, state the verification object, stage, and pass condition.
- If no item needs discussion and the user asked for edits, continue to the final handling phase.

## Inquiry Comments

For comments that ask why something was done, first answer the question in the checklist. Do not automatically write the answer into Markdown, code comments, or docs.

Only modify a file when the answer is useful context for future readers, such as a design reason, constraint, boundary condition, or invariant that the document should preserve.

## Final Status Labels

Use exactly one final status for each comment:

- `已改进文件`: A file was changed to address the comment.
- `已答复，无需改文件`: The comment was answered, but no file change is appropriate.
- `标成待定`: The comment cannot be resolved without more information or a decision.
- `需要后续验证`: The comment depends on later implementation, runtime testing, integration, or rollout verification.

Do not invent additional final statuses. Put nuance in `处理结果` or `理由/验证要求`.

## Final Handling Checklist

After discussion is complete and edits or the second version are produced, return the final checklist:

| 评论编号 | 相关逻辑 | 最终状态 | 处理结果 | 理由/验证要求 | 涉及文件 |
| --- | --- | --- | --- | --- | --- |

Rules:

- Include exactly one row per input comment.
- Keep `评论编号` and `相关逻辑` aligned with the pre-discussion table.
- For `已改进文件`, list the changed file path and concrete change.
- For `已答复，无需改文件`, explain why no file change is appropriate.
- For `标成待定`, state the missing information or decision.
- For `需要后续验证`, state the verification object, stage, and pass condition.
- If one file edit covers multiple comments, keep separate rows and list the same file where relevant.

## Changed Files Summary

After final handling, list every modified file once. For each file, summarize what changed and which comment ids it addresses.

If no files were modified, write `本次没有修改文件。`

## Pending Or Later Verification

List only comments whose final status is `标成待定` or `需要后续验证`. If there are none, write `无。`

## Final Verification

Before finalizing, explicitly check:

- Input comment count equals pre-discussion table row count and final checklist row count.
- Every row has a `相关逻辑` value.
- Related comments are still separate rows and are not merged away.
- Every initial judgment uses one of the four allowed pre-discussion labels.
- Every final status uses one of the four allowed final labels.
- Every rejected comment includes a reason.
- Every later-verification item includes what to verify, when to verify it, and the pass condition.
- Every `已改进文件` row has at least one file path.
- The changed-files summary matches the actual files changed.
- No obvious generated, dependency, log, or unrelated files were modified.