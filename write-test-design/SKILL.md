---
name: write-test-design
description: Write or refine implementation-independent Test Designs for frontend features from a confirmed frontend Spec or equivalent behavior contract. Use when Codex needs to turn acceptance criteria into traceable business scenarios, cover state, failure, boundary, race, consistency, and lifecycle risks, decide whether each scenario should be automated with the existing unit-test framework or verified manually, or audit a Test Design before implementation and unit-test authoring.
---

# Write Test Design

## Goal

Produce a concise, business-readable Test Design that proves the confirmed frontend behavior without following the current implementation's functions or branches. Default to plain Chinese unless the user requests another language.

Do not write test code. Preserve a stable link from each confirmed rule to its test scenarios so implementation and later unit tests cannot silently narrow the coverage.

## Input Gate

Require an approved Frontend Spec or an equivalent confirmed behavior and acceptance contract. The upstream document may be produced by `write-frontend-spec`, but that Skill does not need to exist in the current project.

If the contract is missing, unconfirmed, or contradictory:

- list the exact gaps and affected scenarios;
- return product or contract questions to the user or Spec owner;
- do not turn proposed behavior into a final scenario or acceptance gate.

Use sources in this order:

1. Confirmed user decisions from the current discussion.
2. Approved Spec and acceptance criteria.
3. Latest API, WebSocket, event, and error contracts.
4. Current code and existing tests.

Read code and old tests only to understand observability, existing coverage, and test cost. Never infer target behavior from current branches, mocks, or assertions.

## Workflow

### 1. Fix Scope

- Record the target project, feature, Spec version or path, and confirmation status.
- State test goals and explicit non-goals.
- Use `<feature>-test-design.md` beside the Spec when the user does not provide an output path.
- Preserve unrelated files and do not implement the feature or tests unless separately requested.

### 2. Extract the Verification Model

Extract only confirmed rules:

- user entry and visible result;
- state, transition, identity, permission, and data-source rules;
- API, event, error, retry, timeout, and recovery contracts;
- concurrency, ordering, stale-result, cleanup, and consistency rules;
- compatibility constraints and prohibited side effects.

Reuse architecture boundaries already established by the Spec. Do not redesign component ownership, data flow, interfaces, or fallback policy inside the Test Design.

### 3. Build Traceability

Map every confirmed acceptance rule to at least one `TS-<AREA>-<NNN>` scenario. A scenario may cover several tightly related rules when their relationship is explicit.

Use this matrix:

| Spec source | Business rule or risk | Scenario ID | Priority | Verification method | Status |
| --- | --- | --- | --- | --- | --- |

Use only `已确认` and `待确认` for status. Keep pending rules out of the final acceptance gate.

Set priority by business impact:

- `P0`: the core goal, data correctness, permission boundary, or recoverability would fail.
- `P1`: an important secondary path, common failure, or significant usability behavior would fail.
- `P2`: a low-impact edge, compatibility detail, or supplementary check would fail.

Do not force a fixed number of scenarios per priority.

### 4. Design Risk-Driven Scenarios

Select only categories that can change the business result:

- normal flow;
- failure, permission, empty, and invalid states;
- boundary values, repeated actions, and prohibited side effects;
- state transitions and meaningful state combinations;
- races, out-of-order results, rapid switching, and duplicate events;
- page, cache, event, server, and persistence consistency;
- initialization, refresh, reconnect, fallback, cancellation, unmount, and cleanup;
- historical data, missing fields, gray rollout, and frontend/backend release order.

Avoid Cartesian products and branch-coverage scenarios with no additional business risk. For races, verify the final business state and forbidden side effects, not only callback order.

Write each scenario as:

```markdown
### TS-<AREA>-<NNN> <业务对象> + <条件> + <预期行为>

- 来源：<Spec rule or acceptance item>
- 优先级：P0 | P1 | P2
- 建议验证方式：自动化验证（现有单元测试框架） | 人工验证
- 前置条件：<stable starting state>
- Given：<explicit branch-affecting inputs>
- When：<user action or external event>
- Then：<one observable business result>
- 不应发生：<forbidden side effect, when relevant>
- 所需可控条件：<system boundaries, time, or event order>
```

Keep one business result per scenario. Multiple assertions are allowed only when they jointly prove that result. Make state, permission, quantity, time, and type explicit when they affect the expected behavior.

Use direct Chinese business wording in titles; avoid vague phrases such as `正确处理` or `返回异常`. Test-data helpers may hide fixed paths, tokens, and common initialization that do not affect the decision, but must not hide branch-affecting inputs.

### 5. Choose the Verification Method

Use only two methods for the current workflow:

- `自动化验证（现有单元测试框架）`: the behavior can be reliably simulated and asserted without a real browser, deployed environment, or real multi-service coordination. This includes pure rules, state transitions, component interaction and visible state, and API, WebSocket, time, event, persistence, or cross-module behavior controlled at system boundaries.
- `人工验证`: the behavior cannot be reproduced reliably with the existing unit-test infrastructure and requires a real browser, deployed environment, or several real services.

Treat component testing as an automation technique that may run on the unit-test runner; do not create a separate verification level. Do not require integration-test or E2E tooling. Until such tooling is explicitly introduced, assign anything that the unit-test environment cannot credibly simulate to manual verification.

Specify what must be controlled, not the concrete mock syntax. Mock remote responses, system time, event order, persistence callbacks, or boundary calls when needed. Do not mock the tested business logic or let a mock directly create the business result under assertion.

### 6. Audit Coverage

Before finalizing, verify:

- every confirmed acceptance rule maps to a scenario;
- P0 risks cover the success path and critical failure path;
- async and stateful flows cover necessary races, stale results, and cleanup;
- key intermediate state is checked when later behavior depends on it;
- scenario titles, inputs, and outcomes are understandable without reading code;
- duplicate and no-value scenarios are removed;
- pending decisions are separated from confirmed acceptance gates;
- every scenario has a feasible automation or manual verification method.

## Default Output

Use this outline and remove empty sections:

```text
# <功能名称> Test Design

## 状态与输入依据
## 测试目标 / 非目标
## 风险与优先级
## Spec - Test 追踪矩阵
## 测试场景
### 正常流程
### 异常与边界
### 状态、竞态与一致性
### 生命周期与兼容性
## 测试数据与可控条件
## 验证方式建议
## 待确认与回流 Spec
## 完成检查
```

## Downstream Boundary

Treat a future `write-unit-test` Skill as an optional downstream step, not a dependency. It should read the approved Spec, the entire approved Test Design, current code, and repository test rules, then implement `.test.ts` or `.spec.ts` files.

`TS-*` IDs provide traceability; they do not require one scenario per file or one scenario per test case. One scenario may need several unit tests, and related scenarios may share one `describe`. The unit-test step may refine test organization but must not redefine or silently drop the approved coverage.

Report the output path, confirmed scope, automation/manual split, validation performed, and any question that still blocks a complete Test Design.
