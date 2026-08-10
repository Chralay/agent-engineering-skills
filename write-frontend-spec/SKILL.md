---
name: write-frontend-spec
description: Write or refine implementation-ready frontend Specs from requirements, current repository code, API or WebSocket contracts, and confirmed user decisions. Use when Codex needs to create or restructure a frontend Spec or technical plan, turn ambiguous product behavior into explicit client behavior and contracts, audit a Spec against current code, or prepare a cross-surface frontend change for implementation and acceptance.
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

State overrides explicitly, for example `确认结果优先于需求原文`. Do not invent fields, fallback logic, migrations, or future scope. Ask a focused question only when the answer materially changes the contract; otherwise state the smallest safe assumption.

Treat every normative rule as one of `用户已确认`, `需求或接口已定义`, `代码已验证`, or `待确认`. Never claim code was inspected unless it was actually read. Do not turn an omitted state or behavior into an exclusion, default, or accepted trade-off.

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
3. If unavailable, ask the user whether to introduce it before continuing. Do not install, link, or copy the skill without the user's approval.
4. If the user approves, use the user-approved installation or project-association workflow, then run the reconnaissance before continuing the Spec.
5. If the user declines, continue with this skill's minimal repository tracing and state the evidence limits and remaining unknowns explicitly.

Skip this gate for simple, greenfield, or already-verified behavior. The user does not need to mention both skills in the original prompt. Treat reconnaissance output as current-state evidence, not as authority for target business decisions.

### 1. Establish Scope and Evidence

- Identify the target project, output path, document status, requirement source, and client/service boundary.
- Inspect the real implementation path. Trace `file -> field or state -> consumer -> user-visible effect` instead of stopping at the first definition.
- Separate current behavior, target behavior, confirmed decisions, and unresolved points.
- Keep a compact evidence ledger for material rules. If code or runtime evidence is unavailable, say so instead of reconstructing it from habit.
- Preserve unrelated files. Do not implement the feature unless the user also asks for code changes.

### 2. Lock the Contract

Define the smallest complete set of rules needed for implementation:

- user entry, visible behavior, messages, and permission boundaries;
- state source of truth, identifiers, mappings, and transitions;
- request, response, event, and error semantics;
- loading, disabled, empty, failure, retry, concurrency, and cleanup behavior;
- compatibility and rollout constraints that are actually required.

For async or multi-client flows, also define ordering, idempotency, stale-data handling, reconnect or fallback behavior, and which side makes the final decision.

Do not choose missing batch semantics, eligibility states, partial-failure behavior, refresh timing, selection retention, retry policy, or request shape. List the confirmed portion first, then ask for the decisions that block a complete contract. Do not finalize dependent interfaces or acceptance criteria until those decisions are settled.

### 3. Use the Default Outline

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

### 4. Keep One Source of Truth

- Describe each process or rule completely in one canonical section.
- Add stable anchors for rules referenced from several places; link back instead of restating them.
- Let `关键假设与约束` and `接口` own contract rules. Let `验收标准` test those rules rather than redefine them.
- Keep current-state facts in `现状与问题`; do not mix them with the target design.
- If a confirmed decision replaces old wording, name the replacement once near the beginning and link to its canonical rule.

## Writing Style

- Use short, direct Chinese sentences and precise technical names.
- Name exact files, components, fields, endpoints, event types, statuses, and user messages when known.
- Use tables for mappings, ownership, dependencies, errors, and trade-offs.
- Use Mermaid only when three or more components, branches, or time-ordered steps are materially clearer visually.
- Use TypeScript, JSON, or text blocks for exact contracts; keep examples internally consistent.
- State user-visible behavior before implementation detail.
- Distinguish public contract fields from internal backend states.
- Prefer explicit rules such as `必须`, `只`, `不得`, and `失败时保留` over vague wording such as `适当处理` or `按需刷新`.
- Avoid implementation diaries, speculative abstractions, repeated background, and code listings that do not clarify a contract.

## Completion Gate

Before finalizing, verify:

- Every target behavior is supported by a requirement, confirmed decision, or inspected code path.
- Every `必须`, `只`, `不得`, default, status set, timeout, and error action is traceable to evidence or marked `待确认`.
- Goals, constraints, data structures, interfaces, acceptance criteria, and risks do not contradict one another.
- Each complex flow covers success, failure, loading or disabled state, concurrency, and cleanup where applicable.
- Acceptance criteria are externally observable or mechanically testable and include important negative cases.
- No acceptance item depends on an unresolved decision; keep it pending until the contract is confirmed.
- Cross-references, anchors, code fences, tables, identifiers, and examples are valid.
- The document does not add unused fields, unrequested migration, or speculative future scope.
- The document never claims a file, branch, endpoint, or runtime result was checked when it was not.
- Relevant document checks and `git diff --check` pass when the Spec is written into a repository.

Report the output path, key confirmed boundaries, validation performed, and any remaining decision that blocks implementation.
