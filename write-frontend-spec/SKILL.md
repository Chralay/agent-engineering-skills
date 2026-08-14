---
name: write-frontend-spec
description: Write, review, or refine implementation-ready frontend Specs from requirements, current repository code, API or WebSocket contracts, and confirmed user decisions. Use when Codex needs to create or restructure a frontend Spec or technical plan, clarify ambiguous product behavior before designing implementation details, audit a Spec against current code, apply review decisions consistently across affected sections, or prepare a cross-surface frontend change for implementation and acceptance.
---

# Write Frontend Spec

## Goal

Produce a concise, evidence-backed frontend Spec that another engineer can implement and verify without rediscovering key decisions. Default to plain Chinese unless the user requests another language.

## Source Priority

Resolve conflicts in this order:

1. Confirmed user decisions from the current discussion.
2. Latest requirement, API, backend, or protocol contract.
3. Current repository code and runtime behavior.
4. Older plans, comments, and assumptions.

State overrides explicitly, for example `确认结果优先于需求原文`. Do not invent fields, fallback logic, migrations, or future scope. Ask focused questions when the answers materially change the contract; otherwise state the smallest safe assumption.

Mark every individual decision as one of `用户已确认`, `需求或接口已定义`, `当前代码已验证`, `参考项目行为，仅供参考`, or `Skill 建议，待确认`. Never use one confirmation id to cover a section that mixes sources. Split mixed decisions or mark each table row separately. Never claim code was inspected unless it was actually read. Do not turn an omitted state or behavior into an exclusion, default, or accepted trade-off.

When evidence is incomplete, separate `已确认`, `建议默认值（待确认）`, and `待确认`. Common UX practice is only a proposal, not evidence. Do not write proposed defaults as final requirements or acceptance criteria.

## Workflow

### Architecture Reconnaissance Gate

Before locking the Spec, decide whether the existing implementation needs deeper reconnaissance. Treat the feature as complex when any of these conditions apply:

- the flow crosses several frontend, service, backend, or persistence boundaries;
- state ownership, identities, sources of truth, or active runtime paths are unclear;
- the design depends on asynchronous jobs, WebSockets, retries, concurrency, recovery, or cleanup;
- focused repository tracing cannot establish the current behavior with sufficient evidence.

For a complex feature:

1. Check whether `$feature-architecture-recon` is available to the current Codex session. A shared folder existing on disk does not by itself make the skill available.
2. If available, use it first to establish the current flow, sources of truth, invariants, failure paths, and evidence chains.
3. If unavailable, ask the user whether to introduce it, then stop and wait for the answer before tracing code or drafting section 0. Do not install, link, or copy the skill without the user's approval.
4. If the user approves, use the user-approved installation or project-association workflow, then run the reconnaissance before continuing the Spec.
5. If the user declines, continue with this skill's minimal repository tracing and state the evidence limits and remaining unknowns explicitly.

Skip this gate for simple, greenfield, or already-verified behavior. The user does not need to mention both skills in the original prompt. Treat reconnaissance output as current-state evidence, not as authority for target business decisions.

### 1. Establish Scope and Evidence

- Identify the target project, output path, document status, requirement source, and client/service boundary.
- Inspect the real implementation path. Trace `file -> field or state -> consumer -> user-visible effect` instead of stopping at the first definition.
- Separate current behavior, target behavior, confirmed decisions, and unresolved points.
- Keep a compact evidence ledger for material rules. If code or runtime evidence is unavailable, say so instead of reconstructing it from habit.
- Preserve unrelated files. Do not implement the feature unless the user also asks for code changes.

### 2. Decide Whether Product Confirmation Is Required

Default to a prerequisite confirmation phase when any of these conditions apply:

- business terms, reference-project concepts, or similar interactions are easy to confuse;
- the UI has multiple states such as enabled, disabled, editing, read-only, or replay;
- lifecycle decisions include start, finish, cancel, switch, exit, or failure recovery;
- the same state or data has several consumers;
- user-visible behavior is not yet fully defined.

Skip the phase only for simple, single-path behavior whose requirements are already explicit, and state why it is safe to skip.

### 3. Write the Prerequisite Confirmation in the Target Spec

For the first draft of a feature that requires confirmation, write only metadata and section 0 in the target Spec. Do not expand the full implementation Spec yet:

```markdown
# <功能名称> Spec

> 状态：需求确认中，不可作为实施依据

## 0. Spec 前置确认

### 0.1 实施阶段
### 0.2 术语与参考项目差异
### 0.3 入口与交互矩阵
### 0.4 生命周期矩阵
### 0.5 状态归属与消费边界
### 0.6 Skill 建议方案
### 0.7 待确认问题
### 0.8 确认记录
```

Use user-visible language. Prefer state and behavior matrices that show what the user does in each state, what appears, and what is retained or cleared. Define each ambiguous term with both `用户看到什么` and `不代表什么`.

Select only the matrices the feature needs, and state why an omitted matrix is irrelevant:

- reference-project differences: reference behavior, keep/remove/change decision, source, and open point;
- entry and interaction: page state, control visibility and availability, action, visible result, retention or cleanup, confirmation, and source;
- lifecycle: event, prior state, entry action, active restrictions, normal exit, failure or cancellation rollback, final retained state, and source;
- state ownership: data, source of truth, purpose, creation and release timing, and persistence;
- consumer boundary: whether each view or runtime consumer reads the data, presentation, failure cleanup, and source.

Keep invented entries, switches, defaults, copy, exit semantics, recovery behavior, and delivery phases under `Skill 建议方案` or `待确认问题`. Do not turn reference behavior or common UX into project requirements.

Do not define complete TypeScript interfaces, internal adapters, async identities, concurrency mechanisms, acceptance criteria, or risk lists during this phase. Raise a technical choice in section 0 only when it changes user behavior or a system boundary.

### 4. Pass the Confirmation Gate

Consolidate all blocking questions in one pass under these themes:

1. terms and scope;
2. entry points, controls, and interactions;
3. lifecycle;
4. persistence, historical compatibility, and failure handling;
5. presentation boundaries across pages and runtime consumers;
6. delivery phase and acceptance scope.

Review only section 0 during this gate. Continue to the formal Spec only when the applicable matrices are complete, blocking questions are resolved, Skill proposals remain separated from confirmed decisions, and the user explicitly says the prerequisite confirmation is approved or communicates the same meaning. Record that approval in `0.8 确认记录`. Never infer approval or fill remaining product behavior silently.

Keep prerequisite confirmation review focused on product meaning, interaction, and scope. Reserve formal Spec review for technical feasibility, rule completeness, and consistency.

### 5. Convert the Same File into the Formal Spec

After explicit approval:

1. Change the status to `需求已澄清，待实现`.
2. Expand the ten formal sections in the same file.
3. Reduce section 0 to an `已确认决策索引` containing only decision ids, concise conclusions, sources, links to canonical rules, and affected sections.
4. Put full logic only in the formal sections; rely on Git history for discarded alternatives.

```markdown
## 0. 已确认决策索引

| 编号 | 已确认结论 | 来源 | 正式规则 | 影响章节 |
| ---- | ---------- | ---- | -------- | -------- |
| C1   | <简短结论> | <来源> | [R1](#r1) | <章节> |
```

Treat section 0 as a lookup index, not a second source of truth.

### 6. Lock the Contract

Define the smallest complete set of rules needed for implementation:

- user entry, visible behavior, messages, and permission boundaries;
- state source of truth, identifiers, mappings, and transitions;
- request, response, event, and error semantics;
- loading, disabled, empty, failure, retry, concurrency, and cleanup behavior;
- compatibility and rollout constraints that are actually required.

For async or multi-client flows, also define ordering, idempotency, stale-data handling, reconnect or fallback behavior, and which side makes the final decision.

Do not choose missing batch semantics, eligibility states, partial-failure behavior, refresh timing, selection retention, retry policy, or request shape. List the confirmed portion first, then ask for the decisions that block a complete contract. Do not finalize dependent interfaces or acceptance criteria until those decisions are settled.

Confirm user behavior and product boundaries before designing state, interfaces, or internal mechanisms. Do not freeze adapters, internal identities, snapshots, or concurrency mechanisms while entry points, exit behavior, normal completion, or failure rollback remain unresolved.

### 7. Use the Default Outline

Keep this order unless the feature clearly makes a section irrelevant:

1. `目标 / 非目标`: Fix delivery scope and exclusions.
2. `现状与问题`: Cite exact current files, behavior, and gaps.
3. `架构图与流程图`: Show component ownership, system boundary, and complex sequences.
4. `关键数据结构`: Define shared states, records, identifiers, and mappings.
5. `上下游依赖`: State guarantees required from upstream and impact on downstream surfaces.
6. `关键假设与约束`: Record invariants and decisions that implementation must not reinterpret.
7. `接口`: Define exact paths, parameters, payloads, responses, events, and errors.
8. `Trade-off`: Explain each chosen decision, benefit, and accepted cost.
9. `验收标准`: Express observable, testable outcomes derived from the contract.
10. `风险、兼容性`: Cover rollout order, historical data, fallback limits, and known failure modes.

Before these sections, add a short metadata block when useful:

```markdown
> 状态：需求已澄清，待实现
>
> 需求依据：<latest source>
>
> 适用项目：`<project>`
```

### 8. Keep One Source of Truth

- Describe each process or rule completely in one canonical section.
- Add stable anchors for rules referenced from several places; link back instead of restating them.
- Let `关键假设与约束` and `接口` own contract rules. Let `验收标准` test those rules rather than redefine them.
- Keep current-state facts in `现状与问题`; do not mix them with the target design.
- If a confirmed decision replaces old wording, name the replacement once near the beginning and link to its canonical rule.
- At each reference, state the one result the local reader needs before linking to the full rule. Do not force readers to follow an id just to understand the basic outcome. Acceptance criteria may restate scenario, action, and observable result, but not duplicate internal mechanisms.

### 9. Handle Reviews and Reopened Decisions

Classify review follow-up before editing:

- For an explanation, typo, or isolated sentence change, edit directly and run only focused checks.
- For a new business conclusion already confirmed by the user, record its affected sections and update the canonical rule, dependent interfaces, trade-offs, acceptance criteria, and risks together.
- For an unresolved business question, change the status to `需求重新确认中，不可作为实施依据`, record the decision, alternatives, and affected sections in section 0, and pause dependent formal-section edits until the user confirms it.

After reconfirmation, update all affected sections in one pass and restore `需求已澄清，待实现`.

Match review scope to impact:

1. During prerequisite confirmation, check only section 0, applicable matrices, and the confirmation record.
2. When the formal Spec is first completed, check the whole document once for scope, data, interfaces, lifecycle, acceptance, and risk consistency.
3. For later changes, check the decision index, affected sections, and corresponding acceptance criteria and risks. Recheck the whole document only when the change affects scope, core state ownership, persistence structure, the main lifecycle, or at least three core sections.

## Writing Style

- Use short, direct Chinese sentences and precise technical names.
- Name exact files, components, fields, endpoints, event types, statuses, and user messages when known.
- Use tables for mappings, ownership, dependencies, errors, and trade-offs.
- Use Mermaid when cross-component collaboration, several participants, multiple branches, time ordering, or state transitions are materially clearer visually.
- Use TypeScript, JSON, or text blocks for exact contracts; keep examples internally consistent.
- State user-visible behavior before implementation detail.
- Distinguish public contract fields from internal backend states.
- Prefer explicit rules such as `必须`, `只`, `不得`, and `失败时保留` over vague wording such as `适当处理` or `按需刷新`.
- Avoid implementation diaries, speculative abstractions, repeated background, and code listings that do not clarify a contract.

## Completion Gate

Before finalizing, verify:

- Every target behavior is supported by a requirement, confirmed decision, or inspected code path.
- Every `必须`, `只`, `不得`, default, status set, timeout, and error action has an item-level source or remains marked `待确认`.
- A complex feature does not expand beyond section 0 before explicit user approval, and its draft status says it is not an implementation basis.
- Applicable confirmation matrices and `0.8 确认记录` are complete without requiring readers to reconstruct decisions from the whole document.
- Skill proposals, reference behavior, and common UX remain proposals until the user confirms them.
- The prerequisite confirmation and formal Spec remain in one file; after approval, section 0 is only a decision index.
- Goals, constraints, data structures, interfaces, acceptance criteria, and risks do not contradict one another.
- Each complex flow covers success, failure, loading or disabled state, concurrency, and cleanup where applicable.
- Acceptance criteria are externally observable or mechanically testable and include important negative cases.
- No acceptance item depends on an unresolved decision; keep it pending until the contract is confirmed.
- Cross-references include the necessary local result summary; anchors, code fences, tables, identifiers, and examples are valid.
- The first formal draft received a whole-document consistency check; later review changes received the impact-appropriate focused or full check.
- The document does not add unused fields, unrequested migration, or speculative future scope.
- The document never claims a file, branch, endpoint, or runtime result was checked when it was not.
- Relevant document checks and `git diff --check` pass when the Spec is written into a repository.

Report the output path, key confirmed boundaries, validation performed, and any remaining decision that blocks implementation.
