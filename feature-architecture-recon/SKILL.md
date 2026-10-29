---
name: feature-architecture-recon
description: Trace how an existing feature works across relevant layers using repository evidence. Use to explain behavior, locate ownership and sources of truth, or assess the impact of a proposed change.
---

# Feature Architecture Recon

## Objective

Investigate existing architecture and assess proposed changes before implementation. Establish how the feature works from repository evidence; when a change is proposed, evaluate it against the current contracts and ownership boundaries.

Keep reconnaissance read-only unless the user explicitly requests implementation. When the request is ambiguous, finish the analysis and separate any proposed edits from confirmed current behavior.

## Workflow

1. Define the target and finishing criteria.
   - Restate the requested behavior or question in product terms.
   - Bound the relevant repository, checkout, application, runtime mode, and user flow.
   - Determine the necessary investigation scope from the current question. Finish when key paths are supported by evidence and unknowns affecting the conclusion are stated; inspect failure paths, lifecycle behavior, and architectural risks only when relevant to the question.

2. Read repository guidance and establish the real shape.
   - Read `AGENTS.md` and other applicable local instructions before substantial inspection.
   - Inspect relevant manifests, scripts, submodules or workspaces, and architecture documents.
   - Confirm startup and build paths from imports, scripts, routes, and configuration. Do not infer runtime ownership from directory names alone.

3. Discover the codebase vocabulary from observable anchors.
   - Start with user-facing copy, routes, commands, API paths, logs, errors, events, persisted fields, tests, and file names.
   - Derive internal nouns such as IDs, state fields, config names, route names, services, and storage keys without requiring the user to provide them.

4. Trace the normal flow end to end.
   - Start at the real user, API, CLI, scheduled-job, or event entry point.
   - Follow control and data through UI or caller, state management, transport, adapters, business logic, persistence, workers, external services, and result consumption as applicable.
   - Record the actual parameters and transformations at every relevant boundary.

5. Identify ownership and sources of truth.
   - Determine which layer owns protocol handling, validation, business decisions, persistence, caching, orchestration, and presentation.
   - Distinguish indexes and caches from authoritative object state.
   - Trace how domain objects are located: route params, stable IDs, database keys, file paths, config records, cache keys, or registries.
   - Note duplicated state and explain which producer and consumer make one representation authoritative.

6. Trace failure paths and lifecycle effects when relevant to the question.
   - Inspect permissions, loading and disabled states, retries, timeouts, cancellation, rollback, cleanup, refresh, reconnect, and error reporting where relevant.
   - Verify whether asynchronous calls are awaited, fire-and-forget, queued, or completed by callbacks, polling, events, or WebSockets.
   - Check save, update, delete, and mutation guards at the owning backend or service layer, not only at visible UI controls.

7. Only when the task involves a proposed change, assess contract compatibility, new-field propagation, and ownership.
   - Identify contracts and assumptions that current callers depend on.
   - Follow every new field, flag, identity, or state through its producer, transport, persistence, and consumers.
   - If a local difference requires handling in multiple layers, check whether it can be handled centrally at the boundary responsible for that difference.
   - Preserve established contracts unless the requirement changes them.
   - Distinguish required behavior from optional implementation mechanisms; recommend the smallest design that fits existing patterns.

8. Verify conclusions with concrete evidence.
   - Prefer `rg` or equivalent fast search, direct file reads, focused tests, existing artifacts, and CLI probes.
   - Check the real request, persisted field, generated file, runtime log, or test when available instead of speculating.
   - Cite exact files and symbols, adding line numbers when practical.
   - Label inference explicitly and state what evidence would confirm it.
   - When evaluating an implemented change, separate failures it causes from unrelated baseline debt.

## Search Strategy

Use the following hints only for paths relevant to the question:

- For UI flows, inspect the action handler, state hooks or store, request wrapper, loading and disabled logic, refresh triggers, and displayed result.
- For APIs, inspect route registration, middleware or adapters, controller or handler, service logic, persistence, response shaping, and callers.
- For event-driven flows, inspect publisher, payload, transport, subscriber registration, filtering, state update, and cleanup.
- For file-based flows, inspect path construction, writes, copies or extraction, readers, cleanup, and packaging paths.
- For multi-package repositories, verify package boundaries, dependency direction, submodule state, and the runtime entry that selects each package.
- When search results are noisy, pivot through unique route names, error strings, payload fields, event types, or persisted keys.

## Evidence Discipline

For key conclusions, trace the entry point, fields, cross-layer transfers, consumers, and final effect. Confirm that the path is active in the target runtime and checkout, keeping evidence from different checkouts separate:

`entry or producer -> field/parameter -> boundary or storage -> consumer -> observable effect`

Keep three categories distinct:

- Confirmed current behavior: directly supported by code, configuration, artifacts, or executed checks. State whether the evidence is static or observed at runtime; code inspection alone does not establish runtime verification.
- Inference: a conclusion that depends on an unverified assumption or an incomplete evidence chain.
- Proposal: a recommended future change, not a description of current behavior.

## Output

Respond in the user's language and adapt the structure to the request. Lead with the conclusion and key evidence chains. Include relevant verification results and unknowns affecting the conclusion; add terminology, lifecycle details, or design risks only when they help answer the question. For proposed changes, explain the affected boundaries and recommended minimal direction.

This skill supplies the analysis for implementation. If the user also requests implementation, summarize the edit path and affected boundaries, then continue under that authorization, preserving unrelated changes and validating the affected behavior.

## Guardrails

- Do not present a generic framework pattern as evidence of the repository's actual architecture.
- Do not confuse generated output, vendored dependencies, stale copies, or inactive routes with source-of-truth code.
- Do not broaden the inspected scope after the user fixes a runtime, checkout, feature, or priority boundary.
- Do not recommend a new abstraction, identity system, state machine, or cross-layer field until the existing contract and adaptation cost are traced.
