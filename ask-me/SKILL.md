---
name: ask-me
description: Use when Codex should deliberately clarify requirements before planning, editing, implementing, reviewing, or documenting; especially when the user asks the agent to ask questions, understand the goal, discover constraints, resolve ambiguity, or iteratively refine requirements through numbered questions and user decisions such as "采用", "允许", "不采用", "adopt", "allow", "reject", or custom answers.
---

# Ask Me

Use this skill to run an intentional clarification loop before committing to a plan or implementation. Prefer Chinese when the active conversation is Chinese.

## Core Protocol

1. Start by identifying what is already clear, what is still uncertain, and which uncertainties would change the work.
2. Ask one round of numbered questions containing 5 to 8 questions whenever there are material unknowns.
3. Prefer multiple short rounds over one oversized questionnaire. Continue asking follow-up rounds until no important ambiguity remains.
4. Include a concise recommendation or default beside a question when it helps the user decide.
5. Accept answers by number. Treat replies such as "采用", "允许", "不采用", "adopt", "allow", "reject", "not now", or a custom explanation as decisions for the corresponding numbered item.
6. After each user reply, summarize the decisions that are now settled and ask only the next necessary questions.
7. Stop questioning when the remaining uncertainty is low-risk or can be handled by a conservative assumption. Then state the final understanding and move into planning or execution.

## Question Quality

Ask questions that are:

- Specific enough to change implementation, scope, UX, data model, validation, tests, or delivery.
- Answerable without making the user do unnecessary research.
- Grouped by theme when possible, such as scope, data, workflow, permissions, edge cases, and acceptance criteria.
- Written in numbered form so the user can respond compactly.
- Balanced: include the reason for asking when the tradeoff is not obvious.

Avoid questions that are:

- Purely performative or already answered by context.
- So broad that the user must design the whole solution from scratch.
- More than 8 in one round.
- Blocking when a safe default is obvious.

## Recommendation Style

When giving options, make a recommendation explicit:

```text
1. Should we store one active record or keep history? My recommendation: one active record for this phase, because history is not needed yet.
```

Use the user's decision vocabulary naturally:

```text
You can answer by number with "采用", "允许", "不采用", or your own wording.
```

If the user says "采用" or "adopt" for a recommended item, treat that recommendation as accepted. If the user says "允许" or "allow", treat the proposed behavior as permitted but not necessarily mandatory. If the user says "不采用", "reject", or "not adopted", do not use that recommendation unless later reversed.

## When To Stop Asking

Stop the clarification loop when:

- The objective is clear.
- The implementation or response boundaries are clear.
- Key constraints, data shapes, permissions, and acceptance criteria are clear enough for the task.
- Remaining details can be handled with stated assumptions.

Before moving on, briefly restate:

- Confirmed decisions.
- Open assumptions, if any.
- The next action you will take.

## Behavior During Execution

If new ambiguity appears while working:

1. Continue with safe, reversible work when possible.
2. Ask another 5 to 8 question round only if the ambiguity affects direction, data contracts, irreversible edits, user-facing behavior, or acceptance criteria.
3. If fewer than 5 meaningful questions remain, do not pad the round with low-value questions. State the safe assumption and proceed, or explain why the single issue is genuinely blocking.

## Mini Example

```text
I understand the target is a server-backed device module. Before implementation, I have six questions:

1. Should device code be unique per institution? Recommendation: yes.
2. Should deletion be soft delete? Recommendation: yes, to match existing repository patterns.
3. Should storage planning be embedded or separate? Recommendation: separate collection.
4. Should current temperature be manual for phase one? Recommendation: yes, until IoT status exists.
5. Should I create API wrappers before changing pages? Recommendation: yes.
6. What counts as done: APIs only, or pages wired too?

You can answer by number with "采用", "允许", "不采用", or your own wording.
```
