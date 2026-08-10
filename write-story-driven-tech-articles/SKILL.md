---
name: write-story-driven-tech-articles
description: >-
  Write, outline, rewrite, or review technical articles, engineering explainers,
  tutorials, architecture notes, postmortems, and internal-sharing posts using
  attention-management techniques adapted from Chinese pingshu (评书): a central
  question, progressive disclosure, meaningful suspense, scene-to-mechanism
  transitions, rhythmic recaps, and complete closure. Use when Codex must make
  technical material accurate and easy to understand while also making readers
  want to continue, especially for requests mentioning engaging technical
  writing, storytelling, narrative structure, 评书, 讲清楚技术, or improving a dry
  draft. Preserve lookup-first formats such as API references unless the user
  explicitly asks to turn them into an article.
---

# Write Story-Driven Technical Articles

## Goal

Borrow the attention control of pingshu without imitating its costume. Make the reader continually want the answer to the next real technical question while preserving facts, evidence, constraints, and uncertainty.

## Non-Negotiable Rules

- Treat technical truth as the gate. Never invent an incident, benchmark, quote, motivation, causal link, or implementation detail to make the story smoother.
- Separate verified fact, inference, opinion, and illustrative analogy. Label uncertainty where it affects the conclusion.
- Preserve critical caveats and safety information at the point where readers need them. Never hide them merely to create suspense.
- Use narrative to reveal the mechanism in a useful order, not to delay the answer artificially.
- Prefer concrete actors, state changes, requests, data, and decisions over abstract noun piles.
- Use metaphors only as temporary scaffolding. Return to the exact technical mechanism before drawing conclusions.
- Avoid ornamental pingshu language such as “且听下回分解” unless the user explicitly requests that voice. Transfer the craft, not the mannerisms.

## Workflow

### 1. Establish the writing contract

Determine or reasonably infer:

- target reader and what they already know;
- the problem they care about;
- what they should understand, decide, or do after reading;
- format, length, tone, and evidence expectations.

Ask only when a missing answer would materially change the article. Otherwise state a brief assumption and proceed.

### 2. Build the truth spine

Inspect the provided code, logs, documents, links, tests, or measurements before designing the narrative. For each material claim, track internally:

`claim -> evidence -> interpretation -> boundary/caveat -> reader consequence`

For code-path or architecture writing, follow the full runtime chain instead of stopping at the first definition:

`source -> value/state -> consumer -> side effect -> observable result`

If evidence is missing, narrow the claim or mark it for verification. Do not use narrative confidence to cover an evidence gap.

### 3. Find the “story core”

Express the article in one sentence:

`This article answers [one central question] by tracing [one causal or decision chain].`

Use a real technical tension as the core, such as:

- expectation versus observed behavior;
- symptom versus root cause;
- convenience versus constraint;
- local optimization versus system effect;
- two plausible designs with different tradeoffs;
- a familiar mental model versus the mechanism that actually runs.

If several questions compete, choose one as the main line and demote the rest to supporting sections or a separate article.

### 4. Design the narrative spine

Use this default progression, adapting it to the material:

1. **Concrete opening:** Start with a symptom, decision, surprising trace, costly consequence, or precise question. Keep the opening proportional; do not spend more than roughly 10% of the article before entering the mechanism.
2. **Reader promise:** State what the reader will understand and why it matters. Give the high-level direction early enough to avoid clickbait.
3. **Current mental model:** Show the reasonable first explanation or existing approach.
4. **Complication:** Introduce the evidence, constraint, or edge case that the simple model cannot explain.
5. **Mechanism:** Trace what actually happens, in causal order and with exact technical nouns.
6. **Proof:** Use code, logs, measurements, a minimal example, or a counterexample to validate the mechanism.
7. **Implication:** Explain what changes in design, debugging, operation, or decision-making.
8. **Closure:** Answer the opening question, close every open loop, and state the boundary of the conclusion.

Keep at most one or two meaningful open questions active. Close each one within the next section or two.

### 5. Draft in explanation beats

Build each section from one or more compact beats:

`scene/evidence -> question -> explanation -> consequence -> next question`

- Give each paragraph one main move.
- Introduce a term when the reader first needs it; define it through its effect before expanding it formally.
- Alternate dense mechanism paragraphs with short orientation or consequence paragraphs.
- Let transitions carry logic: explain what the previous section resolved and what remains unresolved.
- Use the narrator's voice to guide attention, correct likely misunderstandings, and recap—not to perform empty banter.
- Turn passive architecture descriptions into observable action when accurate: who sends what, which boundary receives it, what state changes, and what becomes visible.

Read [references/pattern-library.md](references/pattern-library.md) when choosing openings, transitions, narrative arcs, or when revising a draft that still feels flat.

### 6. Match the arc to the article type

- **Mechanism explainer:** surprising behavior -> competing explanations -> causal trace -> evidence -> usable mental model.
- **Tutorial:** desired outcome -> smallest working path -> checkpoints -> failure branches -> final verified result.
- **Architecture note:** user-visible event -> runtime path -> ownership boundaries -> state and side effects -> tradeoffs.
- **Debugging article:** symptom -> evidence timeline -> plausible false leads -> discriminating test -> root cause -> fix and prevention.
- **Design decision:** pressure or constraint -> options -> decisive evidence -> choice -> cost accepted -> revisit conditions.
- **Postmortem:** impact -> timeline -> mechanism -> contributing conditions -> recovery -> prevention; avoid manufacturing a heroic protagonist or a simplistic villain.

### 7. Revise in four passes

1. **Truth pass:** Check every material claim against its evidence. Remove invented connective tissue and overclaimed causality.
2. **Clarity pass:** Define terms, expose hidden prerequisites, shorten overloaded sentences, and keep references unambiguous.
3. **Momentum pass:** Cut throat-clearing, move essential context closer to its use, and make every section change the reader's understanding.
4. **Closure pass:** Resolve the central question, close all legitimate suspense, include limits, and leave the reader with a usable conclusion.

## Output Behavior

- When asked for an article, deliver the article rather than a long explanation of the method.
- When asked for an outline, expose the central question, section questions, evidence assigned to each section, and planned closure.
- When rewriting, preserve the source's verified meaning and required details. Do not create drama by altering facts.
- Keep citations, file paths, measurements, or source notes near the claims they support when traceability matters.
- Put unresolved factual gaps in a short verification note instead of silently smoothing them over.
- Preserve reference-style documents optimized for lookup unless the user explicitly requests an article transformation.

## Final Check

Do not finalize until all answers are yes:

- Is the central technical question clear by the end of the opening?
- Does the article establish why the question matters without exaggerated stakes?
- Does every section advance the causal or decision chain?
- Can the reader distinguish evidence from interpretation and analogy?
- Are technical terms, boundaries, counterexamples, and caveats accurate?
- Does each transition provide a legitimate reason to continue rather than a vague tease?
- Are all opened questions answered or explicitly marked unresolved?
- Does the ending return to the opening and give the reader a usable mental model or next action?
- Does the article still work when read aloud, without bloated setup or monotonous density?
