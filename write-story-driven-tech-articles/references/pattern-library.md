# Pingshu-to-Technical-Writing Pattern Library

Use this reference selectively. Choose only patterns that serve the material; do not force every article to use every technique.

## Technique Mapping

| Pingshu craft | Technical-writing translation | Use it for | Guardrail |
| --- | --- | --- | --- |
| 定场、起势 | Open on a concrete technical moment | A failure symptom, trace, decision, or consequence | Enter the mechanism quickly; avoid a long anecdote |
| 书胆、主线 | Keep one central question | Give the reader a stable reason to continue | Split unrelated questions instead of forcing a grand theme |
| 扣子、关子 | Keep a legitimate question temporarily open | Move from observation to cause or from option to decision | Reveal the answer as soon as the required evidence is available |
| 铺平垫稳 | Supply context just before it becomes necessary | Prerequisites, architecture boundaries, historical constraints | Do not front-load a textbook chapter |
| 抑扬顿挫 | Vary information density and sentence rhythm | Alternate evidence, mechanism, recap, and implication | Do not replace precision with theatrical language |
| 穿针引线 | Let transitions name what changed and what remains | Maintain continuity across technical sections | Avoid empty lines such as “事情没那么简单” |
| 重提、回扣 | Briefly recall the relevant earlier fact | Reorient readers after a dense section | Repeat the conclusion, not whole paragraphs |
| 收口 | Return to the opening question and close loops | Convert understanding into a mental model or action | Do not end with a generic slogan |

## Opening Patterns

Use an opening only when its factual basis exists.

### Observable anomaly

`[Expected behavior] should have happened. The trace shows [different behavior]. The gap points to [central question].`

### Consequential choice

`Both [option A] and [option B] solve the visible problem. Only one preserves [important constraint]. To see why, follow [decisive runtime or data path].`

### Request or data journey

`A user performs [action]. Before the result appears, the request crosses [key boundaries]. The surprising part is where [state/ownership/decision] actually changes.`

### Familiar rule with a boundary

`“[Common rule]” is useful until [specific condition]. Under that condition, [observed consequence] reveals the mechanism the shortcut leaves out.`

### Before-and-after result

`Changing [specific factor] changed [observable result]. The useful lesson is not “always do X,” but why X mattered under [conditions].`

## Logical Transition Patterns

- `This explains [resolved point], but not yet [remaining point].`
- `If [hypothesis] were true, we would observe [discriminating evidence]. Instead, [actual evidence].`
- `The boundary matters because ownership changes here: [before] is responsible for X; [after] is responsible for Y.`
- `At this point the symptom is accounted for. The next question is whether the proposed fix preserves [constraint].`
- `The analogy has done its job; in the real implementation, the exact mechanism is [mechanism].`
- `The first conclusion holds only when [condition]. Outside it, [counterexample or limitation].`

Avoid vague transitions such as “众所周知,” “显而易见,” “让我们拭目以待,” or “事情远没有这么简单” unless the same sentence states the specific evidence or unresolved question.

## Section Rhythm

Use a repeating but non-mechanical rhythm:

1. Show one concrete observation.
2. Ask the smallest useful question raised by it.
3. Explain the mechanism with precise nouns and verbs.
4. Prove or bound the explanation.
5. State why the reader should care.
6. Open the next question only if it follows naturally.

After two or three dense paragraphs, add a short orientation sentence that says what is now known. Do not summarize information the reader has just read unless the summary changes its meaning or prepares the next step.

## Narrative Arc Templates

### Explain a mechanism

`surprising observation -> likely model -> contradicting evidence -> runtime/data trace -> corrected model -> consequences`

### Explain a bug

`symptom -> scope -> evidence timeline -> hypothesis test -> root cause -> fix -> regression proof -> prevention`

### Explain a design choice

`constraint -> viable options -> decision criterion -> evidence -> chosen tradeoff -> rejected costs -> revisit trigger`

### Teach a procedure

`visible goal -> minimum prerequisites -> first working result -> checkpoints -> common failure branches -> verified completion`

## Before-and-After Example

The following is a hypothetical example, not an empirical claim.

**Flat version**

> Clients should use exponential backoff and jitter when retrying failed requests. Exponential backoff increases the delay between retries. Jitter adds randomness and prevents synchronized retries.

**Story-driven version**

> Suppose a thousand clients receive `503` in the same second. If every client retries exactly one second later, the recovering service does not get breathing room; it gets a second synchronized wave. Exponential backoff moves later waves farther apart, but identical clients can still remain in step. Jitter breaks that synchronization by varying each delay. The two mechanisms solve different parts of the same problem: backoff reduces retry frequency, while jitter reduces retry alignment.

Why the revision works:

- It opens with a concrete system state rather than a definition.
- It creates one real question: why is backoff alone insufficient?
- It reveals the mechanisms in causal order.
- It closes the loop with an exact distinction, not a metaphor.

## Failure Modes

- **Fake incident:** Inventing an outage, quote, or benchmark for drama.
- **Clickbait gap:** Hiding a simple answer after a long introduction.
- **Metaphor takeover:** Continuing the analogy after it stops matching the implementation.
- **Humanized machinery:** Giving a component intention when only state and rules are known.
- **Hero-villain postmortem:** Turning system conditions into personal blame.
- **Question spam:** Ending every paragraph with a rhetorical question.
- **Context dump:** Explaining all prerequisites before the reader knows why they matter.
- **Unclosed loop:** Raising an edge case or contradiction and never resolving it.
- **Polished uncertainty:** Writing an unsupported inference with the confidence of a verified fact.

When any failure mode appears, repair the evidence and information order before polishing the prose.
