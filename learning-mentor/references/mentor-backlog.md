# Mentor Evolution Backlog

revision: 3  
updated_at: 2026-08-01

Use statuses `proposed`, `trial`, `adopted`, `rejected`, or `deferred`. Add or change an item only after confirmation.

## Active Items

### MENTOR-001 - Explain New Concepts Before Operations

- status: `adopted`
- problem: A new dependency was introduced through installation commands before the learner received a conceptual explanation of what it was and why it mattered.
- proposed_behavior: For each genuinely new concept, explain what it is, the problem it solves, its position in the current project, and one minimal example before asking for installation, coding, or command execution.
- affected_files: `SKILL.md`, `references/mentor-backlog.md`, and `references/decisions.md`.
- compatibility: Existing commands, progress state, track invariants, confirmation rules, and per-message permissions remain unchanged. Previously confirmed concepts are not mechanically repeated.
- trial_scope: No trial required; the behavior is concrete, low risk, and supported by a real teaching failure.
- decision: Adopted after explicit learner confirmation on 2026-08-01.

### MENTOR-002 - Learner-Facing Study Note Command

- status: `trial`
- problem: Internal progress and learning logs are intentionally concise and do not provide a durable learner-facing document containing corrected misconceptions, repeated confirmation points, and missed boundaries.
- proposed_behavior: Add `note:` to create or incrementally update an explicitly supplied Markdown file or the active track's default note, while preserving learner-authored content and keeping the artifact separate from mentor state.
- affected_files: `SKILL.md`, `references/command-reference.md`, `references/tracks.md`, and `references/mentor-backlog.md`.
- compatibility: `note:` grants current-message edit permission only for the resolved note file. It does not change progress, task completion, tracks, internal logs, verification permission, or ordinary context-loading behavior.
- trial_scope: Use once with an empty Markdown file and once with an existing learner-authored Markdown file, then assess structure preservation, usefulness, deduplication, and path handling.
- decision: Entered trial after explicit learner confirmation on 2026-08-01; adoption remains pending trial evidence.

## Item Format

```text
### MENTOR-<number> - <title>

- status:
- problem:
- proposed_behavior:
- affected_files:
- compatibility:
- trial_scope:
- decision:
```
