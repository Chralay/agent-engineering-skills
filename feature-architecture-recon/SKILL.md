---
name: feature-architecture-recon
description: Reconnoiter how an existing feature actually works before designing, refactoring, reviewing, or implementing changes. Use when Codex must analyze an unfamiliar or partially understood codebase; trace a feature, bug, request, state transition, or data flow across layers; discover project-specific terms and sources of truth; review a proposed design for architectural risks; or produce an evidence-backed implementation plan without expecting the user to know internal identifiers.
---

# Feature Architecture Recon

## Objective

Build an evidence-backed model of the existing feature before recommending or making changes. Discover the repository's own vocabulary, entry points, identities, state, storage, boundaries, side effects, and invariants instead of filling gaps with generic architecture assumptions.

Keep reconnaissance read-only unless the user explicitly requests implementation. When the request is ambiguous, finish the analysis and separate any proposed edits from confirmed current behavior.

## Workflow

1. Define the target and finishing criteria.
   - Restate the requested behavior or question in product terms.
   - Bound the relevant repository, checkout, application, runtime mode, and user flow.
   - Treat the analysis as complete only when entry points, normal flow, sources of truth, boundary contracts, important failure paths, invariants, risks, and remaining unknowns are covered.

2. Read repository guidance and establish the real shape.
   - Read `AGENTS.md` and other applicable local instructions before substantial inspection.
   - Inspect top-level files, manifests, scripts, submodules or workspaces, and architecture documents.
   - Confirm startup and build paths from imports, scripts, routes, and configuration. Do not infer runtime ownership from directory names alone.
   - Preserve checkout boundaries when similar repositories or branches may differ.

3. Discover the codebase vocabulary from observable anchors.
   - Start with user-facing copy, routes, commands, API paths, logs, errors, events, persisted fields, tests, and file names.
   - Search discovered identifiers in both definitions and consumers.
   - Derive internal nouns such as IDs, state fields, config names, route names, services, and storage keys without requiring the user to provide them.

4. Trace the normal flow end to end.
   - Start at the real user, API, CLI, scheduled-job, or event entry point.
   - Follow control and data through UI or caller, state management, transport, adapters, business logic, persistence, workers, external services, and result consumption as applicable.
   - Record the actual parameters and transformations at every boundary.
   - Continue beyond the first request or function until the user-visible result, persisted effect, emitted event, or terminal state is reached.

5. Identify ownership and sources of truth.
   - Determine which layer owns protocol handling, validation, business decisions, persistence, caching, orchestration, and presentation.
   - Distinguish indexes and caches from authoritative object state.
   - Trace how domain objects are located: route params, stable IDs, database keys, file paths, config records, cache keys, or registries.
   - Note duplicated state and explain which producer and consumer make one representation authoritative.

6. Trace non-happy paths and lifecycle effects.
   - Inspect permissions, loading and disabled states, retries, timeouts, cancellation, rollback, cleanup, refresh, reconnect, and error reporting where relevant.
   - Verify whether asynchronous calls are awaited, fire-and-forget, queued, or completed by callbacks, polling, events, or WebSockets.
   - Check save, update, delete, and mutation guards at the owning backend or service layer, not only at visible UI controls.

7. Test the proposed change against existing invariants.
   - Identify contracts and assumptions that current callers depend on.
   - Follow every new field, flag, identity, or state through its producer, transport, persistence, and consumers.
   - Treat widespread propagation of a layer-local concept as adaptation pressure and a possible ownership error.
   - Prefer adapting at the nearest owning boundary and preserving established contracts unless the requirement genuinely changes them.
   - Distinguish required behavior from optional implementation mechanisms; recommend the smallest design that fits existing patterns.

8. Verify conclusions with concrete evidence.
   - Prefer `rg` or equivalent fast search, direct file reads, focused tests, existing artifacts, and CLI probes.
   - Check the real request, persisted field, generated file, runtime log, or test when available instead of speculating.
   - Cite exact files and symbols, adding line numbers when practical.
   - Label inference explicitly and state what evidence would confirm it.
   - Separate failures caused by the proposed change from unrelated baseline debt.

## Search Strategy

- Search product language first, then narrow to discovered symbols.
- Search definitions, producers, serializers, transport fields, persistence writes, readers, and final consumers.
- For UI flows, inspect the action handler, state hooks or store, request wrapper, loading and disabled logic, refresh triggers, and displayed result.
- For APIs, inspect route registration, middleware or adapters, controller or handler, service logic, persistence, response shaping, and callers.
- For event-driven flows, inspect publisher, payload, transport, subscriber registration, filtering, state update, and cleanup.
- For file-based flows, inspect path construction, writes, copies or extraction, readers, cleanup, and packaging paths.
- For multi-package repositories, verify package boundaries, dependency direction, submodule state, and the runtime entry that selects each package.
- When search results are noisy, pivot through unique route names, error strings, payload fields, event types, or persisted keys.

## Evidence Discipline

For important claims, capture the complete chain:

`entry or producer -> field/parameter -> boundary or storage -> consumer -> observable effect`

Do not stop at the first definition, the first request, or a similarly named implementation. Confirm that the inspected path is active in the target runtime and checkout.

Keep three categories distinct:

- Confirmed current behavior: directly supported by code, configuration, artifacts, or executed checks.
- Inference: strongly suggested by evidence but not executed or fully observable.
- Proposal: a recommended future change, not a description of current behavior.

## Output

Respond in the user's language and adapt the structure to the request. Unless the user asks for another format, include:

- Scope and finishing criteria
- Key codebase terms
- Existing end-to-end flow
- Sources of truth and important invariants
- Failure and lifecycle behavior
- Design risks or adaptation pressure
- Recommended minimal direction
- Evidence and verification performed
- Remaining unknowns, only when they materially affect the conclusion

Use exact `file -> symbol or field -> consumer -> effect` chains for the most important findings. Keep the report concise enough to guide a decision.

If implementation is requested, first summarize the concrete edit path and affected boundaries. Then implement only after the architecture risk is sufficiently understood, preserve unrelated changes, and run validation proportional to the affected layers.

## Guardrails

- Do not ask the user for internal names that can be discovered from the repository.
- Do not present a generic framework pattern as evidence of the repository's actual architecture.
- Do not confuse generated output, vendored dependencies, stale copies, or inactive routes with source-of-truth code.
- Do not broaden the inspected scope after the user fixes a runtime, checkout, feature, or priority boundary.
- Do not recommend a new abstraction, identity system, state machine, or cross-layer field until the existing contract and adaptation cost are traced.
- Do not edit code during an analysis-only request.
