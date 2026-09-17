# Command Reference

revision: 3  
updated_at: 2026-09-17

Prefixes express intent and are not strict syntax. Accept equivalent natural language. Unless stated otherwise, commands do not grant persistent permission.

## Contents

1. `main:`
2. `branch:`
3. `status:`
4. `next:`
5. `checkpoint:`
6. `review:`
7. `note:`
8. `adjust:`
9. `track:`
10. `auto:`
11. `verify:`
12. `context:`
13. `handoff:`
14. `resume:`
15. `help:`
16. `evolve:`
17. Confirmation phrases

## `main:`

Purpose: continue the active learning line from the latest confirmed position.

Behavior:

- Read the active track, progress, roadmap, and task queue.
- Restate only the current phase, task, and immediate subgoal.
- Teach one small step and stop at a natural checkpoint.
- For new exercise code, follow the learner-written-code guidance in `SKILL.md`: provide a minimal starting skeleton, hints, and success criteria; give a complete solution only when explicitly requested.
- Preserve all branch questions as side discussions.

Writes: none by default.

Example: `main: 继续学习`

## `branch:`

Purpose: answer a related side question without losing the main line.

Behavior:

- Scope the answer narrowly.
- Explain how the branch connects to the current topic.
- End by naming the preserved main-line task.
- Propose a durable track only if the question becomes a repeated long-term goal.

Writes: none.

Example: `branch: Python async 和 Promise 有什么区别？`

## `status:`

Purpose: show the current learning state without teaching or changing it.

Return:

- Active track and any supporting tracks.
- Current phase and task.
- Current task status and last confirmed checkpoint.
- Known blocker or fragile understanding.
- One exact next action.

Writes: none.

Example: `status:`

## `next:`

Purpose: enter the next already-confirmed task.

Behavior:

- If the current task is confirmed complete, start the next queued task.
- If it is not confirmed complete, request a checkpoint or explicit skip decision.
- Never use `next:` alone to mark mastery, pass a phase gate, or generate a new phase.

Writes: normally none. Advancing the stored pointer requires explicit confirmation if it was not already recorded.

Example: `next: 开始下一项`

## `checkpoint:`

Purpose: assess a task, project milestone, or phase against evidence.

Behavior:

- State what is confirmed, partial, incorrect, or missing.
- Cite the learner's explanation, code, test, or artifact as evidence.
- Repair only the key misconception blocking progress.
- Compare phase checkpoints against documented exit criteria.
- Propose exact changes to progress, tasks, and learning log.
- Wait for confirmation before writing or advancing.

It does not:

- Mark a task complete merely because content was discussed.
- Enter the next phase automatically.
- Treat test success alone as proof of conceptual mastery.

Writes: only after `确认记录` or an equally explicit instruction.

Examples:

- `checkpoint: 检查我是否理解结构化输出`
- `checkpoint: 我完成了前20项，请按阶段验收标准检查`

Related: use `next:` after a recorded task checkpoint; use `handoff:` after a good stopping point.

## `review:`

Purpose: revisit confirmed learning and expose fragile memory or disconnected concepts.

Behavior:

- Read relevant recent log entries and confirmed checkpoints.
- Ask for recall, comparison, prediction, or a small reconstruction.
- Separate forgetting from never-mastered material.
- Propose a bridge task only when review reveals a meaningful gap.

Writes: none unless the learner confirms a proposed gap or bridge task.

Example: `review: 回顾最近完成的5项任务`

## `note:`

Purpose: create or incrementally update a learner-facing Markdown study note. This artifact is for the learner's review and is not ordinary mentor state.

Target resolution:

1. Use an explicitly linked or named `.md` file when provided.
2. Otherwise use the active track's `learner_notes` path from `tracks.md`.
3. Keep an explicit path scoped to the current message; do not silently replace the configured default.
4. For `note: set-default <path>`, propose the `tracks.md` change and wait for confirmation before writing it.
5. If multiple targets are supplied without clear roles, ask which one to edit. Reject non-Markdown targets and request system approval when the resolved path requires it.

Behavior:

- Treat `note:` as current-message authorization to create or edit only the resolved note file.
- Use `note: preview` to show the proposed note changes without writing.
- Create the exact missing `.md` target when authorized. Fill an empty file with a useful learner-note structure.
- Read an existing target before editing. Preserve learner-authored text, headings, and organization; integrate into the narrowest fitting sections and avoid whole-file replacement.
- If safe integration would require destructive restructuring or the learner's intent is ambiguous, show a preview and ask before editing.
- Use current conversation evidence, submitted code and results, confirmed learning state, and the existing note itself. Do not infer events that are absent from available evidence.
- Organize by task or topic and include only useful sections: core concept, misconception or incomplete understanding, why it failed, corrected model, repeated confirmations, missed boundaries and counterexamples, minimal examples, active-recall questions, and unresolved items.
- Distinguish `已修正`, `待复习`, and `待验证`. Do not label an ordinary question or not-yet-taught topic as an error.
- Deduplicate and revise existing entries instead of appending a chronological transcript.
- Exclude secrets, unrelated private information, full conversations, and raw terminal dumps.

It does not:

- Change progress, task completion, track status, internal learning logs, or handoff state.
- Run code, tests, builds, or commands.
- Grant permission to edit source code, other notes, or later messages.
- Load learner notes during normal teaching, review, resume, or handoff unless the learner explicitly includes them in scope.

Writes: the resolved note file for the current message. Changing the default path requires a separate confirmed state update.

Examples:

- `note: [学习笔记.md](E:/notes/学习笔记.md)`
- `note: 更新 [Python笔记.md](E:/notes/Python笔记.md)`
- `note: 把 AI-006 的内容更新到 [Python笔记.md](E:/notes/Python笔记.md)`
- `note: preview [Python笔记.md](E:/notes/Python笔记.md)`
- `note: set-default E:\\notes\\ai-agent.md`

## `adjust:`

Purpose: change the current learning plan, task size, pace, order, project scope, or route.

Behavior:

- Explain the problem being solved and the smallest suitable change.
- Identify affected roadmap, queue, progress, or learner preference files.
- Preserve completed evidence and explain compatibility impact.
- Wait for confirmation and record the reason in `decisions.md`.

Use `adjust:` for the learner's plan. Use `evolve:` for persistent mentor behavior.

Writes: only after confirmation.

Example: `adjust: 最近 Python 任务太大，请拆小`

## `track:`

Purpose: manage durable learning directions.

Operations:

- List tracks and their states.
- Propose a new track with goal and completion criteria.
- Mark a track `supporting`, `paused`, `planned`, or `completed`.
- Switch the one active track.

Rules:

- Keep exactly one active track.
- Do not create a track for a one-off branch question.
- Explain how a new track affects workload and the current main line.

Writes: creating, changing status, or switching requires confirmation.

Examples:

- `track: 查看所有学习方向`
- `track: 创建“系统设计”学习方向`
- `track: 暂停系统设计，返回 AI Agent 主线`

## `auto:`

Purpose: permit requested file creation or modification for the current user message.

Behavior:

- Keep edits within the named task and scope.
- Preserve unrelated user changes.
- Do not run tests, builds, formatters, or examples unless the same message also grants verification.
- Do not carry permission to later messages or a new conversation.
- Do not treat `auto:` as permission to record learning progress unless the message explicitly requests that confirmed state write.

Writes: requested artifact edits only.

Example: `auto: 创建这次练习的项目文件`

## `verify:`

Purpose: permit checks for the current user message.

Behavior:

- Explain the relevant check and expected signal.
- Run only the narrowest useful tests, commands, examples, or file inspections.
- Report results and the smallest next correction.
- Do not edit files unless the same message also grants edit permission.
- Do not carry permission forward.

Writes: no intentional artifact edits; checks may create normal tool caches or test output when unavoidable and in scope.

Examples:

- `verify: 运行现有测试并解释失败`
- `auto: verify: 修复练习并运行相关测试`

## `context:`

Purpose: assess whether the current conversation is becoming too long or complex for reliable continuation.

Behavior:

- Use exact platform metrics only when truly available.
- Otherwise report a qualitative risk based on large artifacts, many branches, repeated summaries, or dependence on early context.
- Recommend a safe stopping point rather than interrupting an atomic task.
- Never fabricate token counts or percentages.

Writes: none.

Example: `context: 检查当前对话是否适合继续`

## `handoff:`

Purpose: prepare confirmed state for a new conversation or another coding agent.

Preview:

- Active track, phase, task, and status.
- Confirmed work completed in this conversation.
- Fragile understanding and unresolved questions.
- Changed files and verification results.
- Important decisions and one exact next action.
- Explicit reminder that temporary permissions do not carry over.

Behavior:

- Show the preview first.
- Wait for `确认交接并记录`.
- Re-read state and check revisions before writing.
- Update progress and log where necessary.
- Replace `session-handoff.md` with the latest confirmed handoff.

Writes: only after confirmation.

Example: `handoff: 准备切换到新对话`

## `resume:`

Purpose: restore the latest confirmed learning state in a new conversation.

Behavior:

- Read profile, tracks, progress, active roadmap and queue, and latest handoff.
- Prefer newer shared state over stale handoff fields.
- Report any inconsistency instead of guessing.
- Summarize the restored position briefly.
- Continue with one exact next step.
- Require fresh authorization for edits or verification.

Writes: none by default.

Example: `resume: 从最近一次交接点继续`

## `help:`

Purpose: list commands or explain one command fully.

Behavior:

- With no argument, return a concise command index.
- With a command name, explain purpose, behavior, non-behavior, write effects, confirmation requirements, examples, and related commands.
- Read this file rather than improvising changed semantics.

Writes: none.

Examples:

- `help:`
- `help: checkpoint`

## `evolve:`

Purpose: propose a persistent change to mentor commands, protocol, routing, files, or invariants.

Behavior:

1. Classify the suggestion: current plan, learner preference, route, or mentor capability.
2. Check whether an existing command can be clarified or extended.
3. Describe the problem, proposal, affected files, compatibility, protected invariants, and examples.
4. Recommend `trial` for uncertain behavior.
5. Wait for `确认采用并更新 Skill` or equivalent approval.
6. Re-read affected files and check revisions.
7. Update core and reference documentation consistently.
8. Validate the Skill and record the decision without committing Git.

Writes: only after explicit confirmation.

Examples:

- `evolve: 为 checkpoint 增加更充分的说明`
- `evolve: 新增一个费曼复述命令`

## Confirmation Phrases

Common confirmations:

- `确认记录`: accept the proposed progress or log update.
- `确认记录并进入下一项`: record completion and advance to the next queued task.
- `确认进入下一阶段并生成下一批任务`: pass the phase gate and create the next rolling batch.
- `确认交接并记录`: write the handoff and related confirmed state.
- `确认采用并更新 Skill`: implement an accepted mentor evolution proposal.

Treat only clear, scoped equivalents as confirmation. A suggestion, question, `checkpoint:`, `adjust:`, `track:`, `handoff:`, or `evolve:` request is not itself permission to write the proposed state.
