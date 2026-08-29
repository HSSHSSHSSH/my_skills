# Learning Log

revision: 23  
updated_at: 2026-08-19

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
