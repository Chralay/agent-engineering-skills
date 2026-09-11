---
name: code-review
description: Review and repair code changes against an approved Spec, confirmed conversation decisions, and optionally an approved Test Design. Use when Codex is asked to review a diff, commit, PR, files, directories, or current task changes; trace real call paths and state contracts; fix and verify P0/P1 defects within scope; inspect directly related production and unit-test code; enforce import-type and minimal-diff rules; and report P2/P3 once without entering polish loops.
---

# Code Review

## Goal

Review the requested code scope against confirmed behavior, fix and verify every in-scope P0/P1 that current authority permits, and report P2/P3 once without continuing the review loop.

Treat invocation of this Skill as authority to make minimal in-scope P0/P1 fixes and adjust directly related unit tests. Do not commit, push, create a PR, change dependencies or frameworks, or expand into unrelated code unless the user separately authorizes it.

Follow this sequence:

```text
establish the review basis and scope
-> trace behavior with code evidence
-> identify, fix, and verify P0/P1
-> recheck only the affected paths and new diff
-> summarize P2/P3 once and stop
```

## Establish the Review Basis

Read repository instructions before reviewing code. Use facts in this order when they conflict:

1. Explicit instructions in the current request.
2. Decisions the user confirmed in the current conversation.
3. The approved Spec introduced for this review.
4. The approved Test Design introduced for this review.
5. Repository rules, interface contracts, and test conventions.
6. Current code, runtime results, and historical tests.

When the user names or supplies an implementation Spec Markdown:

- read it completely;
- extract goals, non-goals, visible behavior, data and state ownership, interfaces, success and failure paths, concurrency and recovery rules, acceptance criteria, prohibitions, and unresolved items;
- use only confirmed rules as defect criteria;
- report any conflict with the current request instead of silently choosing one.

When no Spec is introduced, use only the implementation behavior, decisions, rejected options, tradeoffs, and stop conditions already confirmed in the current conversation. If neither source provides a usable behavior contract, state the missing basis and ask for it. Do not promote personal preferences into P0/P1.

When the user names or supplies an approved `*-test-design.md` produced by `write-test-design`:

- read it completely;
- extract `TS-*` identifiers, sources, priorities, confirmation status, Given/When/Then, forbidden side effects, controllable boundaries, ordering requirements, and verification methods;
- treat only confirmed scenarios marked `自动化验证（现有单元测试框架）` as unit-test coverage requirements;
- do not require `人工验证` or `待确认` scenarios as unit tests;
- use the Test Design to decide how to prove behavior, never to override the Spec's business behavior or contracts.

Ordinary README files, background documents, drafts, proposed rules, and unrelated Markdown are not review baselines merely because they are Markdown.

## Fix the Scope

Set the scope in this order:

1. Files, directories, commits, PRs, or diffs explicitly named by the user.
2. Files created or changed for the same task in the current conversation.
3. Directly related callers, consumers, types, interfaces, configuration, and unit tests.

If the user does not specify a scope, inspect `git status`, the staged diff, and the working-tree diff to identify task-related changes. Preserve unrelated dirty-worktree changes.

Read enough surrounding code to establish entries, callers, input sources, output consumers, state or persistence sources of truth, and normal, failure, cleanup, and concurrency paths. Read outside the edit scope when needed to prove impact, but do not modify unrelated code.

## Build Evidence Before Findings

Trace each target behavior through the real path:

```text
entry or event
-> parameter and state source
-> core decision
-> API, persistence, file, IPC, worker, or process boundary
-> consumer
-> user-visible result or final state
```

Prefer concrete request paths, persisted fields, cache state, callers, consumers, diffs, generated artifacts, and terminal results over speculation.

For every finding, record:

```text
trigger condition -> actual code path -> incorrect result -> affected scope
```

Continue read-only investigation when evidence is incomplete. If the issue remains unproven, report it as needing verification rather than inflating its priority.

## Classify and Converge

Use these priorities:

- `P0`: reachable permanent data loss or unrecoverable overwrite, severe security failure, application or core feature unavailable, production crash, or broad incorrect results without an effective fallback.
- `P1`: confirmed core behavior violation; broken main or important failure path; API, persistence, IPC, or state-contract mismatch; concurrency, retry, cleanup, or authorization logic that overwrites valid state; regression, build/type/test failure introduced by the change; or swallowed critical failure that leaves the system running incorrectly.
- `P2`: real but non-blocking boundary, readability, unnecessary abstraction, over-splitting, or test-maintenance issue that does not break the protected behavior.
- `P3`: naming, comments, formatting, and local style suggestions that do not affect behavior or understanding materially.

Fix in-scope P0/P1 directly with the smallest local change. Iterate until they are resolved or a real blocker requires user choice, new authority, or an external system. Do not fix P2/P3 by default and do not use them to start another review round.

## Apply Mandatory Review Rules

### Use `import type` for type-only imports

Convert symbols used only in TypeScript type positions to type imports:

```ts
import type { IRequestConfigIdentity } from "./globalConfig";
import { mapRemoteTaskStatus, type IRequestConfigIdentity } from "./module";
```

Apply this as a local mechanical correction in scope instead of leaving it for a P2 loop. Preserve ordinary imports for runtime enums, functions, constants, classes, or any symbol that is evaluated at runtime.

### Enforce Spec-defined static contracts

When a confirmed Spec names an exact existing type, generic instantiation, DTO, or file shape, read the complete definition, including required and optional fields plus nested element types. Require the implementation to express an equivalent static type at the layer that owns the output. If importing the original type would cross a repository, submodule, UI, or service boundary, define an equivalent local DTO or move the contract to a neutral shared module; do not erase the contract instead.

Keep `any`, `unknown`, `Record<string, unknown>`, `JsonRecord`, and index signatures at genuinely dynamic input boundaries only. Do not use them as the whole business object, array element, persisted value, or generated-file type when the Spec provides an exact shape. Narrow or validate dynamic input before returning the typed contract. Treat an erased confirmed API, persistence, or file contract as P1 when it can hide missing or invalid output fields. Do not treat `satisfies` as runtime validation or combine it with a preceding broad assertion that makes the check meaningless.

### Check every affected function for over-splitting

For each newly added, extracted, or materially affected function, first ask whether it contains only one logical action. If so, decide whether the function still deserves an independent boundary.

Prefer retaining a function when it encapsulates a repeated business rule, isolates a system boundary, marks a transaction, lock, permission, error-conversion, or cleanup boundary, materially clarifies the main flow, or has multiple real callers.

Prefer inlining when a single-use function only wraps an assignment, forwarding call, or simple condition; has no business, boundary, reuse, or error-handling value; forces needless navigation; anticipates hypothetical reuse; or exists only to make tests easier to mock.

Give one conclusion for every affected function: `保留`, `建议内联`, or `需要调整边界`, with a concrete reason. Treat readability-only over-splitting as P2. Raise it to P1 only when it causes an actual ordering, locking, transaction, cleanup, error, or state-boundary defect.

### Keep implementation boundaries direct

Check for unnecessary helpers, managers, adapters, strategies, or wrapper layers; protocol handling, API adaptation, or business rules placed at the wrong layer; duplicate sources of truth; disk state not synchronized with runtime state; and fields, promises, state, or return values with no consumer.

Require each abstraction to explain in one sentence which real duplication or error risk it removes. Prefer inline code when that value is unclear.

### Match defensive validation to the real trust boundary

Before adding a type guard, schema check, fallback, or catch-and-ignore branch, trace `producer -> transport -> consumer`. Identify the independent source that can produce malformed data and the concrete failure the validation prevents.

Require runtime validation for genuinely dynamic or independently controlled input, such as user input, network responses, persisted files, plugins, multiple producers, deserialization, or cross-version compatibility. Prefer direct typed consumption when one in-repository producer and consumer are deployed together, the transport only forwards the payload, and no legacy or third-party input enters the path.

Do not treat `any` or `unknown` in a weakly typed adapter as proof that runtime data is untrusted. Improve or reuse the owned static contract when practical. Do not repeat the same shape check in every internal consumer or add a partial validator that merely warns and drops an internal event without a defined recovery path.

Keep defensive validation only when its trust boundary, rejected inputs, and recovery behavior can be explained concretely. Treat unnecessary behavior-neutral validation as P2. Classify validation that drops valid state, masks a broken producer contract, or leaves a core flow stale according to its actual impact, including P1 when it violates confirmed behavior.

### Check concurrency and state consistency

For asynchronous, file, cache, Worker, IPC, or multi-process code, determine:

- what each lock protects and which state it does not protect;
- whether request-start snapshots are mixed with response-time current state;
- whether an older failure or result can overwrite a newer success;
- whether persisted state, current-process cache, and other-process caches agree;
- whether initialization, save, or cleanup failure leaves partial state.

Do not accept the presence of a lock or a passing mocked test as proof that runtime state is synchronized.

### Protect the worktree

Make only confirmed P0/P1 fixes, direct test adjustments, and explicit type-import corrections. Do not reformat, reorder, or clean unrelated files. Do not overwrite user changes or use destructive Git commands.

## Review Unit Tests When Relevant

Apply this section when the implementation includes unit tests or the repository contains unit tests directly related to the target code. Do not skip related tests merely because their files were not changed.

Check production code first. Reject production-only promises, delays, callbacks, fields, return values, exports, switches, branches, dependency wrappers, or one-call helpers added solely to make tests easier. Prefer testing through real business entries and controlling only system boundaries. Mark behavior or contract changes as P1; report readability-only test concessions as P2.

### Keep production contracts independent from test convenience

For every production-facing interface, type alias, wrapper, injection point, export, or abstraction added or changed alongside tests:

- identify its production owner, runtime implementation, real callers, and reason to exist independently of tests;
- reject a narrower production signature or exported abstraction when its only purpose is to let a mock implement fewer members or make assertions easier;
- use the real framework, library, domain, or shared type when it is the actual runtime contract and no production substitution boundary exists;
- keep test-only narrowing, builders, fixtures, casts, and doubles in test code, and mock only the system boundary that production actually crosses;
- retain a narrow production port only when it expresses a real production boundary, such as multiple runtime implementations, deliberate dependency inversion, cross-layer ownership, or isolation from a volatile external contract;
- after a narrow production contract is justified, derive it from an importable authoritative type with language-native type composition such as `Pick`, `Omit`, `Parameters`, or `ReturnType` instead of manually duplicating members; do not treat type composition itself as justification for the abstraction;
- define an independent local contract only when importing the authoritative type would violate a repository or layer boundary, and document which production boundary owns that contract.

Treat a test-driven production contract that changes runtime behavior, hides a required contract, or introduces a build/type failure as P1. Treat an unnecessary but behavior-neutral production abstraction as P2, and recommend removing it rather than merely changing its syntax.

Then verify that each related test:

- aligns its title, preconditions, call entry, and assertions with one business behavior;
- obtains the asserted business result from real production logic rather than a mock;
- mocks only network, file, time, Worker, IPC, persistence, or similar system boundaries;
- exposes branch-affecting inputs instead of hiding them in helper defaults;
- awaits promises and genuinely exercises rejection paths;
- restores timers, globals, subscriptions, components, and mocks;
- can fail when the business implementation is wrong and does not merely test the mock or incidental details;
- introduces no warning, unhandled promise, resource leak, or order dependence.

For concurrency, out-of-order, and state-overwrite behavior that the existing unit-test framework can control reliably, require a regression test. Add it when it is missing and directly related to an in-scope P0/P1 fix.

When an approved Test Design is present, trace every in-scope confirmed automated `TS-*` scenario to the relevant test file and test case. Do not silently drop or redefine coverage, and do not force manual or pending scenarios into unit tests.

Do not create tests merely to increase coverage. Never weaken assertions, broaden mocks, add meaningless retries, or use `.skip` to turn tests green. Ask before adding dependencies, changing the test framework or configuration, or making broad production changes for testability.

## Validate and Recheck

Run verification proportional to the risk:

- targeted unit tests;
- directly related module tests;
- type checks, lint, builds, or static checks;
- final diff, generated-file, line-ending, and unrelated-change checks.

Distinguish failures introduced by the current change from existing repository baseline failures. A zero exit code is not sufficient when the change introduces warnings, unhandled promises, leaks, or order dependence.

After fixing, recheck only the affected behavior paths and the new diff for residual or newly introduced P0/P1. Stop after P0/P1 are resolved and P2/P3 have been summarized once.

## Report the Result

Lead with the outcome and the review basis. Include:

1. Whether P0/P1 existed, how many were fixed, and how many remain blocked.
2. A P0/P1 table with priority, trigger and issue, impact, fix, file, and verification; write `未发现 P0/P1` when empty.
3. Real P0/P1 blockers and the minimum user decision or authority needed.
4. One P2/P3 table with issue, impact, suggestion, and file; write `未发现需要汇总的 P2/P3` when empty.
5. A per-file function-boundary table with function or abstraction, whether it has one logical action, conclusion, and reason.
6. The unit-test result: related tests, Test Design path and confirmation status, automated `TS-*` traceability, production concessions, test credibility, modifications, commands, results, and warnings.
7. Every changed file and every verification actually run. Never report an unrun check as passed.

Finish only when the review basis and scope are explicit, each finding has evidence, every authorized P0/P1 is fixed and verified or genuinely blocked, relevant unit tests and automated Test Design scenarios are checked, type-only imports and affected function boundaries are reviewed, P2/P3 are summarized once, and the final diff contains no unrelated change.
