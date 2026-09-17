# Learning Log

revision: 32  
updated_at: 2026-09-17

Record only confirmed learning events. Keep entries concise; do not copy full conversations.

## Entry Format

```text
### YYYY-MM-DD - <track> - <task or milestone>

- Evidence:
- Confirmed understanding:
- Remaining gap:
- Next action:
```

## Entries

### 2026-08-01 - ai-agent - AI-001

- Evidence: Explained the Vue -> FastAPI -> LLM -> controlled-tool flow, including authentication, data minimization, loop limits, and the absence of direct LLM-to-Vue communication.
- Confirmed understanding: The model requests actions; trusted back-end code validates, authorizes, and executes them. Raw SQL and other model output must never be executed automatically, and user confirmation does not replace back-end authorization.
- Remaining gap: Concrete implementation of these boundaries has not yet been practiced; later tasks will provide that evidence.
- Next action: Start `AI-002` by creating and running a minimal Python project and explaining the interpreter and environment.

### 2026-08-01 - ai-agent - AI-002

- Evidence: Located Python 3.13.3 and its CPython executable, created and ran a minimal `main.py`, and reported the actual interpreter path from the running program.
- Confirmed understanding: PowerShell resolves the `python` command to an interpreter through `PATH`; the interpreter reads and executes the source file. Projects using the same base environment can affect one another through shared dependencies.
- Remaining gap: Creating and reproducing an isolated virtual environment is deferred to `AI-005`.
- Next action: Start `AI-003` with a small business-data transformation using lists, dictionaries, loops, and conditions.

### 2026-08-01 - ai-agent - AI-003

- Evidence: Independently implemented and locally ran a business-data transformation that filtered paid orders with amount at least 100 and summed their amounts; submitted source produced `A001`, `A003`, and total `320` by inspection, matching the reported output.
- Confirmed understanding: Used a list of dictionaries, dictionary-key access, a `for` loop, a compound `and` condition, list accumulation, and numeric accumulation correctly; explained why `A005` was excluded.
- Remaining gap: The transformation is still top-level code without function boundaries or type annotations.
- Next action: Start `AI-004` by refactoring business logic into typed functions and explaining inputs, outputs, optional values, and a type mismatch.

### 2026-08-01 - ai-agent - AI-004

- Evidence: Refactored order qualification into a typed function, added a `str | None` function with explicit `None` handling, and reproduced a runtime `TypeError` by passing `str` where `int` was annotated.
- Confirmed understanding: Parameter and return annotations communicate contracts and are machine-readable, but CPython does not enforce them by default. Static checkers can detect incompatible call arguments before execution, while incompatible runtime operations can still raise errors.
- Remaining gap: No static checker or annotation-driven validation framework has been integrated yet; their practical effects will become clearer in later Pydantic and FastAPI tasks.
- Next action: Start `AI-005` by creating a project-local virtual environment and separating reusable code into a module.

### 2026-08-01 - ai-agent - AI-005

- Evidence: Created and activated a project-local `.venv`, compared interpreter paths for `python`, `py`, and `py -3.13`, moved reusable order logic into a module and then a package, and ran the imported function with the expected `True` and `False` results.
- Confirmed understanding: Virtual-environment activation temporarily changes command resolution in the current PowerShell session; a package contains modules, `from order_app.rules import is_qualified_order` imports a function from the package's `rules` module, and `__init__.py` initializes the package rather than serving as this program's entry file.
- Remaining gap: Business data is still represented by ordinary dictionaries without runtime schema validation.
- Next action: Start `AI-006` by defining an order model with Pydantic and examining validation failures for at least two invalid inputs.

### 2026-08-02 - ai-agent - AI-006

- Evidence: Created a project-local virtual environment with Pydantic 2.13.4, defined an `Order` model, validated and dumped a valid order, and observed validation failures for zero quantity and an empty order ID.
- Confirmed understanding: `model_validate()` checks field presence, types, and `Field` constraints before creating a model; `gt=0` rejects zero with `greater_than`, while `min_length=1` rejects an empty string with `string_too_short`. A correct type can still violate a content constraint.
- Remaining gap: Validated data has not yet been serialized to persistent JSON or recovered safely from file failures.
- Next action: Start `AI-007` by writing validated order data to JSON, reading it back without losing expected fields, and then handling a missing or malformed file.

### 2026-08-04 - ai-agent - AI-007

- Evidence: Serialized order data to JSON text and a UTF-8 file, read it back with equal contents, handled a missing file with `FileNotFoundError`, and handled a trailing-comma syntax error with `JSONDecodeError` including its line and column.
- Confirmed understanding: `dumps/loads` convert between Python objects and JSON strings, while `dump/load` write to and read from file objects. File opening and JSON decoding fail at different stages. Dictionary `==` compares contents, whereas `is` compares object identity.
- Remaining gap: Expected failures are still reported with `print`; structured logging, environment-based configuration, business errors, and unexpected errors have not yet been practiced.
- Next action: Start `AI-008` by classifying configuration, business, and unexpected failures, replacing print-only handling with logging, and reading one setting from an environment variable.

### 2026-08-09 - ai-agent - AI-008

- Evidence: Read a required file path from a process-scoped environment variable, raised and handled a custom `ConfigurationError`, logged successful and missing configuration without exposing its value, modeled a refund-rule rejection with `BusinessError` and `WARNING`, and recorded an unexpected `ZeroDivisionError` with `logger.exception()` and its traceback.
- Confirmed understanding: Configuration errors come from missing or invalid external settings, business errors express expected domain-rule failures, and unexpected errors need diagnostic detail. An unhandled exception propagates up the call chain and terminates the program with a traceback, while an exception caught by `except` can be logged and allow execution to continue when it is not raised again.
- Remaining gap: No HTTP request has yet been made or handled for success, timeout, and non-success status paths.
- Next action: Start `AI-009` by learning the parts of an HTTP request and response, then call one public API and handle success, timeout, and a non-success response.

### 2026-08-09 - ai-agent - AI-009

- Evidence: Called a public JSON API with HTTPX, received and decoded a `200` response, observed an external `503`, handled a deliberate `404` through `HTTPStatusError`, and handled a forced request timeout through `TimeoutException` without an unhandled traceback.
- Confirmed understanding: `response.status_code` represents the received HTTP status, `response.json()` decodes JSON into a matching Python structure, and `raise_for_status()` converts non-success responses into `HTTPStatusError`. A timeout occurs while waiting for network work and may happen before a complete `Response` is assigned, so it is distinct from receiving an HTTP error status.
- Remaining gap: Synchronous and asynchronous waiting have not yet been compared, and there is no confirmed explanation of when `await` releases control or when async is not useful.
- Next action: Start `AI-010` with a minimal sequential waiting example, then compare it with concurrent asynchronous waiting.

### 2026-08-09 - ai-agent - AI-010

- Evidence: Ran two sequential `await asyncio.sleep()` calls in about 2.02 seconds, ran two coroutines concurrently with `asyncio.gather()` in about 2.02 seconds instead of 3 seconds, and demonstrated that replacing asynchronous sleep with `time.sleep()` made the same gathered tasks execute sequentially in about 3.00 seconds.
- Confirmed understanding: The event loop schedules runnable Tasks; an unfinished asynchronous `await` can suspend the current Task and return control to the loop, while `asyncio.gather()` schedules multiple coroutines for concurrent progress. Synchronous blocking work such as `time.sleep()` prevents the event loop from scheduling an already-ready Task, so `async def` alone does not make blocking code asynchronous or faster.
- Remaining gap: No automated tests have yet been written with pytest for happy and failure paths.
- Next action: Start `AI-011` by learning what an automated test checks, then write and run one minimal happy-path pytest test.

### 2026-08-11 - ai-agent - AI-011

- Evidence: Created two ordinary Python functions, wrote a happy-path assertion for `calculate_total()`, wrote a failure-path test for `validate_quantity()` with `pytest.raises()`, and ran both tests successfully under Python 3.13.3 with pytest 9.1.1.
- Confirmed understanding: Pytest discovers files and functions by their test naming conventions. A test expecting an exception passes when the required exception and message are produced; if no exception is raised, pytest marks that test as failed rather than treating the business failure path itself as a failed test.
- Remaining gap: No web endpoint has yet been created, and the responsibilities of a FastAPI route and its response have not been practiced.
- Next action: Start `AI-012` by learning what a FastAPI route and response are, then create and run one minimal GET endpoint.

### 2026-08-11 - ai-agent - handoff after AI-011

- Evidence: Confirmed and recorded `AI-006` through `AI-011`, then prepared a cross-conversation handoff at the natural boundary before `AI-012`.
- Confirmed understanding: Pydantic validation, JSON persistence, exception and logging categories, HTTP success/error/timeout paths, event-loop scheduling versus blocking work, and pytest happy/failure paths have confirmed practice evidence.
- Remaining gap: FastAPI routes and responses have not yet been practiced; event-loop terminology and expected-exception testing should continue to be reinforced during API work.
- Next action: In a new conversation, run `resume: 从最近一次交接点继续`, then start `AI-012` with the FastAPI route and response concepts before creating the first GET endpoint.

### 2026-08-12 - ai-agent - AI-012

- Evidence: Created an isolated `ai-012` environment with FastAPI 0.141.1 and Uvicorn 0.52.1, ran a `GET /hello` endpoint, and received `HTTP/1.1 200 OK` with `content-type: application/json` and the expected `{"message":"Hello, FastAPI"}` body.
- Confirmed understanding: A route combines an HTTP method and URL path and maps matching requests to a handler function. FastAPI serializes the returned Python dictionary into the JSON response body, while the complete HTTP response also includes a status code and headers. An unregistered `POST /hello` does not invoke the registered GET handler.
- Remaining gap: A JSON request body has not yet been mapped into a Pydantic model or rejected through FastAPI validation.
- Next action: Start `AI-013` by defining a Pydantic request model, using it in a POST endpoint, and comparing valid and invalid requests.

### 2026-08-12 - ai-agent - AI-013

- Evidence: Added a `POST /orders` endpoint with an `OrderCreate` Pydantic request model, received `200` with the accepted order for valid input, and received `422` with a `greater_than` validation error for `quantity=0`.
- Confirmed understanding: FastAPI parses the JSON request body and validates it against the annotated Pydantic model before calling the endpoint handler. Valid input becomes an `OrderCreate` object, while invalid input prevents the handler from running; the validation response identifies the failing location as `body.quantity` and the violated `gt=0` constraint.
- Remaining gap: The API has no explicit business failure response, and its success, validation-failure, and business-failure paths are not yet covered by automated tests.
- Next action: Start `AI-014` by distinguishing schema validation from business-rule rejection, then add one useful API error and tests for all three paths.

### 2026-08-12 - ai-agent - AI-014

- Evidence: Added a `409 Conflict` response for an insufficient-stock business rule, manually observed the expected response, then used FastAPI `TestClient` and pytest to cover the `200` success, `422` request-validation failure, and `409` business-failure paths; all three tests passed under Python 3.13.3 and pytest 9.1.1.
- Confirmed understanding: Pydantic request-model validation occurs before the endpoint handler and can produce `422`, while a valid request can enter the handler and then be rejected by a business rule with `HTTPException`. `TestClient` exercises the FastAPI application in-process without a running Uvicorn server, and a failure-path test passes when the API returns the expected error response.
- Remaining gap: The API has not yet been called from a browser-based Vue page, so loading, success, error, and cross-origin behavior have no integration evidence.
- Next action: Start `AI-015` by understanding the browser-to-FastAPI origin boundary, then connect a Vue page and display loading, success, and error states.

### 2026-08-12 - ai-agent - AI-015

- Evidence: Added FastAPI CORS middleware for the explicit Vue development origin, reran the existing API suite with all three tests passing, created a Vue 3 and TypeScript Vite front end, and called `POST /orders` from the browser. The page displayed a disabled `提交中…` button while waiting, a success message for `quantity=2`, `Insufficient stock` for `quantity=11`, and the Pydantic validation message for `quantity=0`; the button returned to its enabled state after each response.
- Confirmed understanding: An origin is defined by scheme, host, and port, so the Vue and FastAPI development URLs are cross-origin. CORS is enforced by browsers and permits an allowed front end to read responses; it is not authentication or business authorization and does not govern curl or in-process pytest requests. A single request-state value keeps loading, success, and error UI states mutually exclusive.
- Remaining gap: FastAPI has not yet called a model provider, and there is no evidence that a model API key stays server-side or that model-call latency is measured.
- Next action: Start `AI-016` by defining the browser, FastAPI, and model-provider trust boundary, then make one minimal server-side model call and record its latency.

### 2026-08-15 - ai-agent - AI-016

- Evidence: Kept the Claude API key in a back-end `.env`, installed the Anthropic Python SDK, and called `claude-sonnet-4-6` asynchronously. A standalone diagnostic call returned the expected text in 4140.21 ms. The FastAPI `POST /generate` path then returned `200` with `ok`, a measured latency of 8133.63 ms, and provider-reported usage of 969 input tokens and 4 output tokens; the existing three API tests still passed.
- Confirmed understanding: Browser code is not secret storage, so Vue sends only user input while FastAPI reads and attaches the provider key. The model-call timer belongs around the back-end provider request rather than the entire browser interaction. Anthropic responses contain content blocks that must be inspected for text. A server-side response-model mismatch can produce `500` after the provider call has already succeeded.
- Remaining gap: System, user, and assistant message roles have not yet been explained or compared. The local `.env` is plaintext and its Git-ignore status has not been verified, so it must not be committed before an ignore rule is in place.
- Next action: Start `AI-017` by defining the three message roles and predicting the effect of changing each role in one controlled example.

### 2026-08-16 - ai-agent - AI-017

- Evidence: Correctly predicted that changing the system instruction changes the response format, that removing prior user and assistant turns loses the order `A001` context, and that a user request alone cannot cause a refund. Repaired two boundary points by explaining that Claude API calls are stateless and that authorized back-end code, not the model, performs consequential actions.
- Confirmed understanding: In Claude Messages API, the top-level `system` parameter sets behavior for the current request and must be resent on later calls. `user` carries the current request, while prior `assistant` messages represent model-produced conversation history rather than verified facts or permissions. The model may request an action or tool call, but the back end independently checks identity, authorization, and approval before executing it. Sonnet 4.6 requests must not end with an assistant prefill.
- Remaining gap: Model output is still treated as free text; no schema has yet constrained or validated its structure, and no invalid model output has been deliberately detected.
- Next action: Start `AI-018` by comparing free text with structured output, then validate one model result with Pydantic and test an invalid result.

### 2026-08-16 - ai-agent - AI-018

- Evidence: Defined a Pydantic `TicketClassification` with literal category and urgency values. The configured Claude-compatible proxy returned Markdown instead of schema-constrained JSON during `messages.parse()`, and Pydantic rejected it with `json_invalid`. After switching to an application-level fallback, a real model response produced valid JSON and became a typed `TicketClassification`; a deliberate `urgency="urgent"` case was rejected at `urgency` with `literal_error`.
- Confirmed understanding: JSON syntax, schema validity, and business usability are separate checks. A prompt requesting JSON can reduce formatting failures but cannot establish trust; Pydantic validation is the application gate. HTTP `200` does not make an incomplete, refused, or schema-invalid result usable. The custom `ANTHROPIC_BASE_URL` proxy does not demonstrate Anthropic's provider-level structured-output guarantee, so synthetic data and explicit local validation remain necessary.
- Remaining gap: Model responses are still buffered until completion; no FastAPI-to-Vue incremental rendering or interruption handling has been practiced.
- Next action: Start `AI-019` by comparing buffered and streamed responses, then implement chunk forwarding from Claude through FastAPI to Vue with an interruption path.

### 2026-08-16 - ai-agent - AI-019

- Evidence: Added an Anthropic text stream behind FastAPI `StreamingResponse`; `curl -N` received `200` with incrementally displayed plain text. The Vue client read `response.body` with a reader, decoded chunks through streaming `TextDecoder`, appended them reactively, showed correct loading and completion states, and reported no console error. An `AbortController` stopped an in-progress request while preserving partial output, and a subsequent request completed normally without unhandled browser or FastAPI errors.
- Confirmed understanding: Stream chunks are transport fragments rather than guaranteed JSON values, words, or sentences, so the client incrementally decodes and appends them. Browser abort can propagate cancellation through FastAPI toward the provider when resources are closed correctly, but it cannot undo tokens already generated or consumed.
- Remaining gap: No deterministic read-only business tool has yet been defined or tested independently of the model.
- Next action: Start `AI-020` by defining a clear input and output schema for one synthetic-record query tool, then implement and test its deterministic behavior without calling the model.

### 2026-08-19 - ai-agent - AI-020 and Agent Core Loop gate

- Evidence: Implemented a standalone `get_order` tool over frozen synthetic order records with Pydantic input and output models. Direct calls returned the expected existing and not-found results without starting FastAPI or calling Claude. Five focused tests covered success, not found, determinism, invalid input, and immutability; the combined FastAPI and tool suite passed all eight tests with one non-blocking TestClient deprecation warning.
- Confirmed understanding: A client tool is ordinary back-end code with an explicit contract. Claude may request a tool, but FastAPI validates, authorizes, and executes it; `get_order` and its backing data provide order facts. Pydantic models validate in Python, while their JSON Schema representation can describe tool inputs to Claude. A client-tool loop continues on `stop_reason="tool_use"`, ends normally on `stop_reason="end_turn"`, and handles other stop reasons separately.
- Remaining gap: No real Claude `tool_use` response, dispatcher, `tool_result` round trip, guarded loop, approval-protected write, or replayable run has yet been implemented.
- Next action: Enter Phase 3 and start `AI-021` by observing one real `get_order` tool request without automatically executing it.

### 2026-08-19 - ai-agent - handoff after AI-020

- Evidence: Confirmed and recorded AI-012 through AI-020, passed the Agent Core Loop gate, generated AI-021 through AI-030, and prepared a cross-conversation handoff at the Phase 3 boundary. The latest local regression result was eight passing FastAPI and tool tests with one non-blocking TestClient deprecation warning.
- Confirmed understanding: Claude may request tools, while trusted back-end code validates, authorizes, and executes them; business data and deterministic tools provide facts. Tool loops continue on `tool_use`, finish normally on `end_turn`, and handle other stop reasons separately.
- Remaining gap: A real `tool_use` response, dispatcher, `tool_result` round trip, guarded loop, approval-protected write, and replayable trace have not yet been implemented. The third-party proxy's tool-use compatibility and `.env` Git-ignore status remain unverified.
- Next action: In a new conversation, run `resume: 从最近一次交接点继续`, then start `AI-021` by observing one real `get_order` tool request without automatically executing it.

### 2026-08-29 - ai-agent - AI-021

- Evidence: Sent the `get_order` tool definition through the configured Claude-compatible endpoint and observed `stop_reason="tool_use"` with one real tool-use block: ID `call_fDatveVgqmZ2u3r1UrYp38d2`, name `get_order`, and input `{"order_id": "ORD-1001"}`. The diagnostic executed no tool automatically.
- Confirmed understanding: Tool input is model-generated and untrusted. The back end must allowlist the tool, validate arguments, authorize access, and only then execute it. Missing `tool_result` proves no result was returned to Claude, while non-execution is established by the absence of a tool call in the application path. Unauthorized requests must not execute or disclose data and should return a controlled error result.
- Remaining gap: The Pydantic input model's generated JSON Schema has not yet been compared with Claude's `input_schema`, and no dispatcher or `tool_result` round trip exists.
- Next action: Start `AI-022` by generating and inspecting the `get_order` input JSON Schema and explaining its relationship to Pydantic runtime validation.

### 2026-08-29 - ai-agent - AI-022

- Evidence: Generated `GetOrderInput.model_json_schema()` and inspected an object schema with required string field `order_id` and `minLength: 1`. Compared it with the manually maintained Claude `input_schema` and identified that the generated schema included `minLength` but omitted `additionalProperties: false`, while the manual schema had the opposite difference.
- Confirmed understanding: JSON Schema is a description generated from the trusted Pydantic model and supplied to Claude as guidance. Claude returns untrusted tool arguments rather than a schema, so the back end must pass `block.input` to `GetOrderInput.model_validate()` for actual runtime validation. Independently maintained schemas can drift.
- Remaining gap: No tool registry or dispatcher exists, so a known tool cannot yet be selected safely and an unknown name is not yet rejected in code.
- Next action: Start `AI-023` by implementing a registry and dispatcher for `get_order`, with an explicit unknown-tool failure path.

### 2026-08-29 - ai-agent - AI-023

- Evidence: Implemented `TOOL_REGISTRY`, `dispatch_tool()`, and `UnknownToolError` in `tool_dispatcher.py`. Added focused tests proving that `get_order` dispatches to the expected deterministic function and that unregistered `delete_order` is rejected; `python -m pytest -q test_tool_dispatcher.py` passed both tests in 0.08 seconds.
- Confirmed understanding: A Python function's existence does not authorize model access. The registry is an explicit allowlist of capabilities available to the model, and the dispatcher must reject names outside it instead of dynamically resolving arbitrary functions.
- Remaining gap: The dispatcher currently accepts an already-created `GetOrderInput`; raw model arguments are not yet validated at the dispatch boundary or formatted into deterministic success and error `tool_result` content.
- Next action: Start `AI-024` by validating raw arguments with Pydantic and formatting controlled serializable results for both valid and invalid input.

### 2026-08-29 - ai-agent - AI-024

- Evidence: Implemented `tool_runner.py` to validate raw `get_order` arguments before dispatch and serialize deterministic success or controlled error `tool_result` blocks. Added focused valid- and invalid-input tests; `python -m pytest -q test_tool_runner.py` passed both tests in 0.07 seconds.
- Confirmed understanding: Claude-generated tool arguments are untrusted and must pass Pydantic validation before dispatch. Validation failure returns a controlled error without executing the tool. The original `tool_use_id` correlates each `tool_result` with its `tool_use`, and fixed JSON serialization supports stable tests and logs.
- Remaining gap: The generated `tool_result` has not yet been sent back to Claude in a real follow-up request, so no grounded final response has been observed.
- Next action: Start `AI-025` by preserving Claude's assistant tool-use message, executing its validated request, and sending the correlated `tool_result` in a follow-up user message.

### 2026-08-30 - ai-agent - AI-025

- Evidence: Completed a real two-request Claude tool round trip. The first response stopped with `tool_use` for `get_order({"order_id": "A001"})`; the Python back end returned a correlated successful `tool_result` containing status `paid` and amount `199.0`. The second response stopped with `end_turn` and grounded its final answer in those exact tool facts.
- Confirmed understanding: Claude requests the action while Python application code validates and executes the tool. Claude Messages API requests are stateless, so the follow-up request must resend the relevant user message, the complete assistant tool-use response, and the correlated user `tool_result`; it does not require unbounded unrelated history.
- Remaining gap: The working path is still written as one fixed two-request sequence rather than a reusable loop that branches on each response's stop reason.
- Next action: Start `AI-026` by extracting the request-and-stop-reason handling into a minimal loop that supports both direct `end_turn` and one `tool_use` path.

### 2026-08-30 - ai-agent - AI-026

- Evidence: Implemented `agent_loop.py` as an application-owned loop with explicit `end_turn` and one-tool `tool_use` branches. Used a fake Claude client to test a direct answer and a tool request followed by a final answer; `python -m pytest -q test_agent_loop.py` passed both tests in 0.91 seconds and verified the follow-up message structure.
- Confirmed understanding: `stop_reason` selects the application branch, while the `return` inside the `end_turn` branch actually terminates the Python loop. A `tool_use` response appends the assistant request and correlated tool result before the next iteration. Without a guard, repeated tool requests can create unbounded latency and token cost while withholding a final response from the user.
- Remaining gap: The loop has no maximum-step or repeated-call guard, and unknown tools, invalid arguments, and tool exceptions are not yet handled through a complete controlled-failure policy.
- Next action: Start `AI-027` by adding a configurable maximum-step limit and a test proving that repeated `tool_use` responses stop deterministically.

### 2026-08-31 - ai-agent - AI-027

- Evidence: Added a configurable maximum-step guard, semantic repeated-call detection using tool name plus canonicalized arguments, controlled unknown-tool and invalid-argument results, and controlled handling for tool execution exceptions. `python -m pytest -q test_agent_loop.py` passed all seven tests in 0.85 seconds.
- Confirmed understanding: A changing `tool_use.id` correlates requests and results but cannot identify semantic repetition; canonical JSON makes equivalent arguments comparable. Unregistered tools must not execute, invalid arguments must fail before dispatch, and recoverable failures should return correlated error `tool_result` blocks. Detailed exceptions belong in developer logs, while Claude receives a generic failure message without internal details.
- Remaining gap: The Agent still has only one read-only query tool; no deterministic report tool, approval-protected write, or replayable run exists yet.
- Next action: Start `AI-028` by defining explicit input and output schemas for a deterministic order-report tool, then implement and test it without calling Claude.

### 2026-08-31 - ai-agent - handoff after AI-027

- Evidence: Confirmed and recorded AI-021 through AI-027, completed the real tool-use round trip and guarded Agent loop, updated the learner note, and prepared a cross-conversation handoff at the clean boundary before AI-028. The latest Agent loop suite passed all seven tests in 0.85 seconds.
- Confirmed understanding: Claude requests tools while trusted Python code allowlists, validates, executes, and returns correlated results. Semantic repetition uses the tool name plus canonicalized arguments rather than the changing tool-use ID. Recoverable tool failures return controlled results; internal exception details stay in developer logs.
- Remaining gap: No deterministic report tool, approval-protected idempotent write, or replayable run exists yet. The current loop and runner are still specialized to `get_order`, and `.env` Git-ignore status remains unverified.
- Next action: In a new conversation, run `resume: 从最近一次交接点继续`, then start AI-028 by defining the smallest useful order-report input and output schemas without calling Claude.

### 2026-09-17 - ai-agent - AI-028

- Evidence: Implemented OrderReportInput and OrderReportOutput with OrderStatus and a nonnegative order_count constraint, plus generate_order_report() over fixed ORDERS in E:/code/S/ai/python-learning/ai-012-020/order_tools.py. Code inspection confirms that the function reads orders without modifying them and does not call Claude. Five new report tests cover matching counts, zero matches, deterministic results, invalid input status, and negative output counts; the learner reported 10 passed in 0.08 seconds for python -m pytest -q test_order_tools.py, including the five existing query tests.
- Confirmed understanding: A valid status with no matching orders returns a normal zero-count report; an unsupported status fails Pydantic validation before the function runs. The returned status preserves the input condition. Model field names and local variable names can differ; pytest.raises fails if the expected exception is absent.
- Remaining gap: Continue reinforcing model keyword names and exception assertions through practice. The report tool is not yet integrated into the dispatcher, runner, or Agent loop; approval-protected idempotent writes and replayable runs remain outstanding.
- Next action: AI-029 is current but not started. When the learner resumes, first explain approval and idempotency and define the smallest consequential write operation before implementation.
