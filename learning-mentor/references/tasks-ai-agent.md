# AI Agent Rolling Task Queue

revision: 21  
updated_at: 2026-08-19

## Queue Rules

- Keep the next actionable batch small; normally add about 10 tasks after a confirmed phase gate.
- Complete tasks in order unless an explicit adjustment is confirmed.
- Use `checkpoint:` before marking a task complete.
- Do not treat reading alone as evidence when the task expects explanation or working code.

## Initial Sequence

| id | status | task | completion evidence |
|---|---|---|---|
| `AI-001` | `completed` | Draw and explain the responsibilities of Vue, FastAPI, the model, and executable tools. | A correct diagram or verbal explanation including trust boundaries. |
| `AI-002` | `completed` | Create and run a minimal Python project. | A locally executed program and an explanation of the interpreter and environment. |
| `AI-003` | `completed` | Practice Python lists, dictionaries, loops, and conditions. | Solve a small business-data transformation without copying a full solution. |
| `AI-004` | `completed` | Write functions with type annotations. | Explain inputs, outputs, optional values, and one type error. |
| `AI-005` | `completed` | Use modules, packages, and a virtual environment. | Explain imports and reproduce the environment setup. |
| `AI-006` | `completed` | Define and validate business data with Pydantic. | Reject at least two invalid inputs and explain the validation messages. |
| `AI-007` | `completed` | Read and write JSON safely. | Preserve expected data and handle a missing or malformed file. |
| `AI-008` | `completed` | Add exceptions, logging, and environment variables. | Distinguish configuration, business, and unexpected errors. |
| `AI-009` | `completed` | Call one public HTTP API from Python. | Handle success, timeout, and a non-success response. |
| `AI-010` | `completed` | Learn one minimal `async` and `await` example. | Explain when waiting releases control and when async is not useful. |
| `AI-011` | `completed` | Test two ordinary Python functions with pytest. | Passing happy-path and failure-path tests. |
| `AI-012` | `completed` | Create the first FastAPI GET endpoint. | A working endpoint and correct explanation of route and response. |
| `AI-013` | `completed` | Create a POST endpoint with a Pydantic request body. | Valid input succeeds and invalid input is rejected. |
| `AI-014` | `completed` | Add useful API errors and tests. | Tests cover success, validation failure, and one business failure. |
| `AI-015` | `completed` | Call FastAPI from a Vue page. | The UI displays loading, success, and error states. |
| `AI-016` | `completed` | Call one model API through the back end. | The key remains server-side and one response is recorded with latency. |
| `AI-017` | `completed` | Explain system, user, and assistant messages. | Predict how changing each role affects one controlled example. |
| `AI-018` | `completed` | Produce and validate structured model output. | Invalid output is detected rather than silently trusted. |
| `AI-019` | `completed` | Stream a model response from FastAPI to Vue. | The UI renders incremental output and handles interruption. |
| `AI-020` | `completed` | Implement one read-only tool over synthetic business records. | The function has a clear schema, deterministic behavior, tests, and no model dependency. |

## Gate After AI-020 - Passed 2026-08-19

Verified before generating the next batch:

- Vue calls FastAPI successfully.
- FastAPI calls one model safely.
- Streaming and structured output work.
- One deterministic read-only tool exists.
- Basic error handling and tests exist.
- The learner can explain every component's responsibility.

The gate passed with confirmed integration evidence, a deterministic read-only tool, eight passing local tests, and a corrected explanation of model, application, schema, and tool responsibilities.

## Agent Core Loop Batch

| id | status | task | completion evidence |
|---|---|---|---|
| `AI-021` | `current` | Observe and explain one real Claude `tool_use` response for `get_order`. | Record `stop_reason`, tool-use block ID, name, and input, while executing nothing automatically. |
| `AI-022` | `queued` | Generate and inspect the `get_order` tool JSON Schema. | Explain the relationship between the Pydantic input model, JSON Schema, and runtime validation. |
| `AI-023` | `queued` | Implement a tool registry and dispatcher. | Dispatch a known tool to the correct function and reject an unknown tool name in tests. |
| `AI-024` | `queued` | Validate tool arguments and format a deterministic `tool_result`. | Valid input produces a serialized result and invalid input produces a controlled error result. |
| `AI-025` | `queued` | Complete one manual `tool_use` to `tool_result` round trip. | Claude requests `get_order`, the back end executes it, and a follow-up response ends with a grounded answer. |
| `AI-026` | `queued` | Implement the minimal Agent loop. | The loop handles a direct `end_turn` and a one-tool path without framework automation. |
| `AI-027` | `queued` | Add loop guards and controlled failure handling. | Tests cover maximum steps, repeated calls, unknown tools, invalid arguments, and tool exceptions. |
| `AI-028` | `queued` | Add a deterministic read-only report tool. | The report tool has a schema, fixed-data behavior, and tests independent of the model. |
| `AI-029` | `queued` | Add a consequential write tool with approval and idempotency protection. | No write occurs without explicit approval, and retries do not duplicate the action. |
| `AI-030` | `queued` | Record, test, and replay one complete Agent run. | A trace reproduces messages, tool requests, results, stop reasons, and the final outcome. |
