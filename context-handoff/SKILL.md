---
name: context-handoff
description: Assess whether a long-running task should move to a fresh context because earlier decisions may be lost, compressed, contradicted, or hallucinated; when a switch is warranted, produce a copyable handoff prompt containing the verified agreements and next objective. Use for context-length checks, continuity audits, or requests to start a new task without losing established decisions.
---

# Context Handoff

Evaluate continuity risk from the conversation that is actually available. Do not claim to know an exact token count, context-window percentage, or hidden truncation state unless the runtime explicitly provides it.

## Decide Whether to Switch

Recommend a fresh context when one or more of these conditions materially threaten correctness:

- earlier requirements or decisions are no longer recoverable with confidence;
- summaries or compaction have replaced details needed for the next step;
- the conversation contains unresolved contradictions, repeated corrections, or signs that prior facts are being confused;
- the task has changed scope several times and stale instructions may contaminate the next action;
- the next step is high-risk or detail-sensitive and depends on a large, scattered history;
- a clean handoff would be more reliable than continuing to reconstruct state in place.

Do not recommend switching merely because the conversation feels long. If the active goal, controlling constraints, current state, and next step are still clear and mutually consistent, say that a new context is not currently needed.

If evidence is mixed but no immediate switch is necessary, report that a switch is not required yet and identify the concrete event that should trigger a later handoff.

## Audit the State

Before answering, reconstruct only what the visible conversation supports:

1. The user's active objective.
2. Explicit agreements, decisions, constraints, and rejected alternatives that still govern the work.
3. Work already completed and its observable results.
4. The current state, including relevant files, identifiers, commands, or errors when present.
5. The next concrete objective and its completion condition.
6. Open questions, blockers, and uncertain facts.

Treat explicit user decisions and verified tool results as authoritative. Do not turn tentative suggestions into agreements. Do not invent missing details to make the handoff look complete; label them as `待确认` or the equivalent in the user's language.

## Respond

Use the user's language. Lead with one of these outcomes in natural wording:

- `不需要新开上下文`
- `建议尽快切换，但当前仍可继续`
- `需要新开上下文`

Give brief evidence for the decision. When no switch is needed, do not generate a handoff prompt unless the user explicitly asks for one anyway.

When a switch is needed, provide one self-contained prompt in a fenced code block that the user can copy into a new task. The prompt must:

- state that it is a continuation of an earlier task;
- state the active objective;
- list the verified agreements and constraints that remain in force;
- summarize completed work and current state;
- state the next objective as a concrete action with a clear success condition;
- list unresolved or uncertain items separately;
- instruct the next agent to preserve the agreements, verify uncertain items before relying on them, inspect referenced artifacts when available, and avoid redoing completed work;
- avoid unnecessary conversational history, obsolete branches, speculation, and unsupported claims.

Prefer a compact, operational handoff over a transcript summary. Include exact paths or identifiers only when they are visible and relevant. If critical details cannot be recovered, say so both in the decision and inside the prompt.
