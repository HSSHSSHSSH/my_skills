# Latest Session Handoff

revision: 4
updated_at: 2026-08-31

- source_agent: Codex Desktop
- active_track: `ai-agent`
- phase: `phase-3` - Agent Loop From First Principles
- current_task: `AI-028`
- current_task_status: `not_started`
- last_confirmed_checkpoint: `AI-027`

## Completed In Session

- Completed, reviewed, confirmed, and recorded `AI-021` through `AI-027`.
- `AI-021`: observed a real Claude `tool_use` response without automatic execution.
- `AI-022`: generated and inspected the `GetOrderInput` JSON Schema and separated schema guidance from runtime validation.
- `AI-023`: implemented `TOOL_REGISTRY`, dispatcher behavior, and unknown-tool rejection.
- `AI-024`: validated raw model arguments and generated deterministic success and error `tool_result` blocks.
- `AI-025`: completed a real `tool_use -> tool_result -> end_turn` round trip for order A001.
- `AI-026`: implemented a minimal application-owned Agent loop and fake-client tests.
- `AI-027`: added maximum-step and semantic repeated-call guards plus controlled unknown-tool, invalid-argument, and tool-exception handling.
- Updated `E:/code/S/ai/note.md` with the confirmed AI-021 through AI-027 concepts and review questions.

## Confirmed Understanding

- Claude requests actions; trusted Python back-end code validates, authorizes, and executes tools.
- JSON Schema guides Claude, while Pydantic `model_validate()` enforces runtime input constraints.
- `TOOL_REGISTRY` is the model-callable capability allowlist; the existence of a Python function does not authorize it.
- `tool_use_id` correlates a result with one request, but semantic repetition is detected with the tool name and canonicalized input.
- `sort_keys=True` makes equivalent dictionaries produce stable serialized fingerprints.
- Unknown tools and invalid arguments must not execute. Recoverable failures return correlated error `tool_result` blocks instead of aborting the entire loop.
- Detailed tool exceptions serve developers through logs; Claude receives a generic failure result without internal details.
- Maximum steps and repeated-call detection do not replace write-operation approval, idempotency, or side-effect deduplication.

## Fragile Or Unresolved

- Python API fluency is still developing; continue reinforcing JSON serialization, pytest monkeypatching, and narrow exception boundaries through use.
- `agent_loop.py` and `tool_runner.py` remain specialized to `get_order`; AI-028 will require gradual generalization for a second tool.
- `agent_loop.py` has non-blocking duplicate-import, indentation, and blank-line cleanup opportunities.
- The configured third-party Claude-compatible proxy has demonstrated tool use with synthetic data, but use only non-sensitive data.
- The back-end `.env` is plaintext and its Git-ignore status remains unverified; do not commit it until an ignore rule is confirmed.
- Approval-protected idempotent writes and replayable run traces remain for AI-029 and AI-030.

## Changed Files

- `E:/code/S/ai/python-learning/ai-012-020/claude_test.py`
- `E:/code/S/ai/python-learning/ai-012-020/schema_test.py`
- `E:/code/S/ai/python-learning/ai-012-020/tool_dispatcher.py`
- `E:/code/S/ai/python-learning/ai-012-020/test_tool_dispatcher.py`
- `E:/code/S/ai/python-learning/ai-012-020/tool_runner.py`
- `E:/code/S/ai/python-learning/ai-012-020/test_tool_runner.py`
- `E:/code/S/ai/python-learning/ai-012-020/agent_loop.py`
- `E:/code/S/ai/python-learning/ai-012-020/test_agent_loop.py`
- `E:/code/S/ai/note.md`
- Mentor state after confirmation: `progress.md` revision 32, `tasks-ai-agent.md` revision 28, `learning-log.md` revision 31, and this handoff revision 4.

## Verification Results

- A real Claude round trip returned order A001 with status `paid` and amount `199.00`.
- `python -m pytest -q test_tool_dispatcher.py`: 2 passed in 0.08 seconds.
- `python -m pytest -q test_tool_runner.py`: 2 passed in 0.07 seconds.
- `python -m pytest -q test_agent_loop.py`: 7 passed in 0.85 seconds.
- Verification evidence came from the learner's local runs; no project code was executed while preparing this handoff.

## Decisions

- Keep exactly one active track: `ai-agent`.
- Let the learner write and run code by default; require fresh per-message `auto:` or `verify:` permission.
- Keep the loop application-owned and explicit before introducing an Agent framework.
- Treat model tool input as untrusted; allowlist names, validate arguments, and return correlated results.
- Detect semantic repeated calls without using the changing `tool_use.id`.
- Return controlled errors to Claude while keeping internal failure details in developer logs.

## Exact Next Action

In a new conversation, run:

`[$learning-mentor](C:\Users\33567\.agent\my_skills\learning-mentor\SKILL.md) resume: 从最近一次交接点继续`

Then start `AI-028` with one small step:

1. Explain what makes a report tool deterministic and read-only.
2. Define the smallest useful order-report input and output schemas.
3. Do not call Claude yet.
4. The learner writes and runs code by default.

## Permission Reset

No previous `auto:`, `verify:`, `note:`, file-edit, or command-execution permission carries into the new conversation. Require fresh authorization for any edit or verification. Do not store an API key or other secret in the handoff.
