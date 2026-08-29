# Confirmed Decisions

revision: 2  
updated_at: 2026-08-01

## 2026-07-31 - Initial Mentor Design

1. Use one reusable personal mentor Skill rather than a static document alone.
2. Keep the mentor protocol generic while using AI application and Agent engineering as the first active track.
3. Store only learning-relevant profile information.
4. Use project-led learning with necessary theory interleaved.
5. Use sequence continuation instead of rigid weekly goals or make-up work.
6. Let the learner write and run code by default; keep `auto:` and `verify:` scoped to one message.
7. Require a proposed update and explicit confirmation before changing progress, routes, tracks, handoffs, or mentor rules.
8. Use rolling task batches and phase gates instead of preplanning the entire route as fixed tasks.
9. Keep one active track and distinguish durable tracks from short branch questions.
10. Share one source directory across Codex and Claude Code through platform discovery links.
11. Preserve only a concise learning log and the latest session handoff, not full transcripts.
12. Assess context health after checkpoints and before phase changes; never invent exact context percentages.
13. Support controlled mentor evolution with detailed help, proposals, trials, confirmation, validation, and Git history.
14. Do not auto-commit changes to the Skill repository.

### 2026-08-01 - Explain New Concepts Before Operations

- context: The learner was asked to install Pydantic before receiving an explanation of the concept and requested a durable teaching rule.
- alternatives: Keep the existing general intuition-first rule, store only a learner preference, or add a concrete ordered protocol to the mentor Skill.
- decision: Add a five-part sequence for genuinely new concepts: explain what it is, the problem it solves, its project position, and one minimal example before moving into operations.
- reason: The ordered protocol prevents unexplained terminology from becoming a prerequisite for action while preserving the small-step teaching style.
- affected_files: `SKILL.md`, `references/mentor-backlog.md`, and `references/decisions.md`.

## Decision Entry Format

```text
### YYYY-MM-DD - <decision>

- context:
- alternatives:
- decision:
- reason:
- affected_files:
```
