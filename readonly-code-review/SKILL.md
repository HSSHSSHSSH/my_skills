---
name: readonly-code-review
description: Conduct scoped, read-only code reviews when the user asks to review code, 审查代码, code review, review a file, or 再次审查. Require approval for every file-reading scope, report numbered findings, and never edit unless the user's message explicitly includes `auto`.
---

# Read-only Code Review

Use this workflow for collaborative code review. Treat the user as the implementation owner or as a reviewer working with another coding agent.

## Non-negotiable rules

- Remain read-only by default. Do not edit files, stage changes, or run mutating commands.
- Edit only when the user's current message explicitly includes `auto` and clearly scopes the requested change. Otherwise provide suggestions or code snippets only.
- Do not read code before the user approves its exact file scope. Do not expand to related files without a new approval.
- Do not run tests by default; mention unrun tests and other unreviewed areas in the result.
- Do not guess unclear business intent. Ask the user or provide a prompt they can send to the implementation agent.

## Review workflow

1. Announce the review focus in commentary.
2. Before any code read, list every proposed file as an absolute clickable path and wait for an explicit approval such as “执行” or “批准”.
3. Read only the approved files. If additional context is needed, stop and request approval for the additional paths.
4. Compare the code with approved specifications and existing conventions. Prioritize data correctness and safety, architecture, edge cases, tests, then readability.
5. Return findings first. Number every finding as `1.`, `2.`, `3.` and include priority (`P0`–`P2`), exact file and line, problem, reason, and suggested correction. Do not modify code.
6. If no actionable issue is found, explicitly say the review passed and state what was not read or tested.

## Output conventions

- Lead with the conclusion.
- Use concise Chinese when the user writes in Chinese.
- Use absolute clickable file links.
- For a follow-up review, reassess the current file contents rather than repeating resolved findings.
- Separate confirmed defects from assumptions. If a repository or dependency was not approved for reading, say so instead of asserting behavior that depends on it.

## Unclear intent

When the code's intent cannot be determined from the approved files and specifications, stop short of a verdict and offer a prompt such as:

```text
请说明 <文件/函数> 的预期业务语义，尤其是：
1. <具体状态、并发或数据一致性问题> 应如何处理？
2. 失败时是否应写入记录或返回业务失败？
3. 是否已有对应的接口契约或业务文档？请给出路径或规则。
```

## `auto` change mode

When and only when the user explicitly includes `auto`, restate the scoped files to be changed, apply the minimal requested patch, and verify proportionally. Preserve unrelated working-tree changes.
