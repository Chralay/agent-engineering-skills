---
name: write-unit-test
description: Write or refine Vue and React frontend unit tests from an approved frontend Spec, approved Test Design, existing production code, and current repository test tooling. Use when Codex needs to implement traceable TS-* automation scenarios, add or reorganize component, state, or business-logic tests, audit mocks and test data, diagnose contract-versus-code failures, or produce a verified unit-test implementation report.
---

# Write Unit Test

Implement approved frontend automation scenarios as maintainable unit tests. Preserve the confirmed business contract; do not redefine it to fit current code.

## Check the input gate

Require:

- An approved Frontend Spec or equivalent business contract.
- An approved Test Design.
- Existing production code for the target behavior.
- The repository's current test runner, commands, and conventions.

Read the complete Test Design, including goals, non-goals, risks, traceability matrix, all scenarios, test data, controllable conditions, manual checks, and unresolved items.

Use this source priority:

1. Decisions confirmed by the user in the current discussion.
2. Approved Spec.
3. Approved Test Design.
4. Current repository rules and production code.
5. Existing tests and historical behavior.

If the Spec and Test Design conflict, keep the Spec expectation and record the Test Design conflict. If code or old tests conflict with approved behavior, do not weaken the assertion or invent a mock to make the test pass.

Default to every automation scenario in the Test Design unless the user narrows the scope. Keep manual and out-of-scope scenarios visible in the report. If an input is missing, a scenario remains unresolved, or production code does not exist, record the affected item as blocked instead of inventing behavior. This version does not run a TDD workflow.

## Apply fixed rules

These rules are part of this Skill and apply across repositories:

- Support Vue and React frontend unit tests.
- Make the behavior named by a test pass through the real tested entry and real business functions.
- Mock system boundaries only. Never mock the business decision, state merge, permission check, retry policy, mapping, or final business result under test.
- Allow mocks or controls only for:
  1. Remote responses and persistence callbacks, including success, failure, timeout, and out-of-order delivery.
  2. System-boundary call arguments, counts, and cancellation when the call itself is part of the contract.
  3. Time, timers, and event order needed to control a race; the mock must not simulate the business result.
- Make test parameters visible. Explicitly pass every value that determines a business branch or expected result inside the test. Do not hide such values in helper defaults.
- Let factories provide only business-irrelevant data such as fixed paths, tokens, IDs, and common initialization.
- Use `[TS-*] + 中文业务标题` for every test title.
- Keep exactly one file-level parent `describe` in each test file. Preserve nested business subgroup `describe` blocks when useful.
- Treat warnings, unhandled promises, open handles, timer leaks, retained subscriptions, and residual mocks as failures to finish.

Adapt to the repository's Vue or React stack, Vitest or Jest runner, component test library, DOM environment, file locations, suffixes, commands, helpers, and naming conventions. Repository conventions may fill in implementation details but must not silently weaken the fixed rules. If an explicit higher-priority repository instruction conflicts, stop on the affected scenario, record the conflict, and ask the user which rule should govern.

## Follow the workflow

### 1. Inspect repository rules

Read `AGENTS.md`, project documentation, package scripts, test configuration, adjacent tests, shared factories, boundary adapters, and cleanup patterns. Reuse existing tools instead of introducing parallel helpers or a new framework.

When `$feature-architecture-recon` is already available, use it for a complex execution path if helpful. It is optional: never install, copy, or require it. Either way, trace enough code to understand the target behavior.

### 2. Trace observable behavior

For each in-scope automation scenario, trace:

```text
user action or external event
-> public entry or component
-> real state and business decisions
-> system boundary or side effect
-> user-visible result or final business state
```

Identify the responsible module, branch-driving inputs, controllable system boundaries, observable results, meaningful intermediate states, races, late results, timers, and lifecycle cleanup.

Do not use private-method call counts as the primary result. Assert a call only when it is itself an external contract or proves a required side effect.

### 3. Create the implementation report

Before editing tests, create `<feature>-unit-test-report.md` beside the Test Design. Derive `<feature>` from the Test Design filename by removing `-test-design`; if the filename is ambiguous, use the documented feature name.

Include:

- Source documents and requested scope.
- A complete implementation mapping.
- Changed test files.
- Commands, results, and warnings.
- Manual, out-of-scope, and blocked scenarios.
- Any production-code, dependency, or configuration change.

Use this mapping:

| Spec source | Rule or risk | Priority | TS scenario | Verification | Test file | Group or test | Main assertion | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |

Use only these statuses:

- `待实现`
- `已覆盖`
- `阻塞`
- `人工验证，不实现`
- `本次范围外`

Count an existing test only when its business conditions and expected result are equivalent. Executing the same function or branch is insufficient.

### 4. Organize tests by responsibility

Place tests with the module that owns the behavior. Do not create one file per `TS-*` number.

Related scenarios may share a file, nested `describe`, setup, and focused helpers. By default, keep independent business results in separate tests so failures remain diagnosable. Split one scenario into multiple tests when it has separately observable results, reusing the same `TS-*` number. Combine multiple scenario numbers only when they genuinely prove one business result, and list every number in the title.

### 5. Write trustworthy tests

- State the business condition and expected result in a direct Chinese title after `[TS-*]`.
- Prove one business result per test; use multiple assertions only when they jointly prove that result.
- Write branch-driving state, permissions, quantities, types, and time values explicitly in the test body.
- Exercise the real business path and observe public behavior or final state.
- For races, control event order and assert the final state plus side effects that must not occur.
- For multi-stage behavior, assert only meaningful intermediate states.
- Keep new helpers single-purpose and readable.
- Restore fake timers, globals, mocks, subscriptions, and mounted components after every test.

Do not collapse unrelated outcomes into one test, mock a business boundary, write the asserted result directly into component or store state, use `.skip`, or loosen assertions to match an incorrect implementation.

### 6. Handle blockers without changing the contract

Default to modifying test code and the implementation report only. Stop expanding the change and record the scenario as `阻塞` when:

- Production behavior conflicts with the approved Spec or Test Design.
- Trusted verification requires a production-code or testability refactor.
- Current tools cannot simulate an approved automation scenario.
- A new dependency, test environment, or global configuration is required.

Record `scenario -> code evidence -> expected -> actual -> required change`, then ask the user before modifying production code, dependencies, or configuration.

### 7. Verify and finish the report

Run, in order:

1. Every new or modified target test file.
2. Directly related module tests.
3. Wider tests, type checks, or builds required by repository rules and change risk.
4. Output inspection for failures, stderr, warnings, unhandled promises, timers, open handles, and retained mocks.

A zero exit code with a change-related warning is not complete. Do not update snapshots blindly, add retries, or increase waits to hide instability.

Update the implementation report with final statuses, exact test names, executed commands, results, warnings, blockers, and whether production code, dependencies, or configuration changed.

## Completion gate

Finish only when:

- Every in-scope automation scenario is `已覆盖` or has a concrete `阻塞` record.
- The full Test Design scenario list remains traceable, including manual and out-of-scope items.
- Each covered scenario maps to a test file and `[TS-*] + 中文业务标题` test.
- Manual checks are not represented by skipped or fake automated tests.
- Named business behavior runs through real business logic.
- Branch-driving inputs are visible in each test.
- Mocks do not produce the asserted business result.
- Target checks pass without change-related warnings or resource leaks.
- Unrelated files remain unchanged.

Report the changed test files, report path, `TS-*` coverage, commands and results, blockers, manual items, and any authorized production or tooling changes.
