# AI Application and Agent Engineering Roadmap

revision: 1  
reviewed_at: 2026-07-31

This is a directional 9-12 month roadmap, not a deadline calendar. Continue the sequence after pauses. Interleave roughly three engineering tasks with one theory task when appropriate.

## End Goal

Build and explain a deployable enterprise workflow Agent that can understand a task, call controlled tools, query knowledge, request approval before writes, preserve run state, produce traces, and pass a repeatable evaluation set.

Use one evolving project rather than unrelated demos:

- Front end: Vue 3 and TypeScript.
- AI service: Python and FastAPI.
- Storage: SQLite first, PostgreSQL when persistence needs grow.
- Runtime: one stable model API first.
- Deployment: Docker.
- Frameworks: implement one minimal tool loop before adopting an Agent SDK.

## Phase 1 - Python and AI Back-End Foundation

Learn:

- Python data structures, functions, modules, typing, classes, and environments.
- Pydantic models, JSON, exceptions, logging, HTTP, async, and pytest.
- FastAPI routes, validation, error responses, and Vue integration.

Deliver:

- A Vue page calling a tested FastAPI service over a small synthetic business dataset.

Exit criteria:

- Independently implement GET and POST endpoints with validation and useful errors.
- Explain the basic difference between synchronous and asynchronous execution.
- Read a normal typed Python project structure without relying on full-copy tutorials.

Primary reference: https://fastapi.tiangolo.com/tutorial/

## Phase 2 - LLM Application Primitives

Learn:

- Tokens, context, message roles, instructions, generation controls, and model limits.
- Structured output, schema validation, streaming, embeddings intuition, retries, timeouts, cost, and secret handling.

Deliver:

- A FastAPI-backed model call with streaming UI, validated structured output, and basic request telemetry.

Exit criteria:

- Call one model without an Agent framework.
- Distinguish free text from validated structured output.
- Handle timeout, provider error, and invalid model output.

## Phase 3 - Agent Loop From First Principles

Learn:

- Tool/function calling, schemas, dispatch, tool results, loop termination, state, maximum steps, read/write boundaries, and human approval.

Deliver:

- A manually implemented loop with at least three business tools: query records, generate a report, and create or update a task.

Exit criteria:

- Explain that the model requests actions while application code executes them.
- Prevent infinite loops and repeated writes.
- Require approval before every consequential write.
- Replay one complete run from recorded events.

After understanding the loop, evaluate a lightweight Agent SDK. Reference: https://openai.github.io/openai-agents-python/

Market checkpoint:

- Around the equivalent of months 4-6, prepare an early project description and test relevant roles without resigning or waiting for total mastery.

## Phase 4 - Retrieval and Reliable Agent Engineering

Learn:

- Parsing, chunking, embeddings, retrieval, citation, refusal, and retrieval evaluation.
- Durable state, memory boundaries, prompt injection, tool authorization, idempotency, retries, tracing, cost, and regression evaluation.

Deliver:

- Add a cited knowledge-search tool and at least 30 fixed evaluation tasks to the main Agent.

Exit criteria:

- Compare results after a model or prompt change.
- Distinguish retrieval failure from generation failure.
- Audit and safely recover consequential actions where practical.
- Locate a failure using traces.

Tracing reference: https://openai.github.io/openai-agents-python/tracing/

## Phase 5 - MCP and Stateful Orchestration

Learn:

- MCP hosts, clients, servers, tools, resources, prompts, stdio, and Streamable HTTP.
- State machines, persistence, interruption, recovery, manager patterns, and handoffs.

Deliver:

- Expose one business capability through an MCP server and invoke it from the Agent.
- Add resumable state to one long-running workflow.

Exit criteria:

- Explain what MCP standardizes and what it does not.
- Justify single Agent, fixed workflow, or multi-Agent design using concrete requirements.

References:

- https://modelcontextprotocol.io/docs/learn/architecture
- https://docs.langchain.com/oss/python/langgraph/overview

## Phase 6 - Portfolio, System Design, and Job Validation

Deliver:

- A deployable demo, short demonstration video, architecture diagram, clear README, Docker setup, evaluation results, and documented failure cases.
- A resume version aimed at AI application or Agent engineering roles.

Exit criteria:

- Explain why the product needs an Agent instead of only a fixed workflow.
- Explain approvals, loop control, RAG evaluation, traces, latency, cost, and single- versus multi-Agent choices.
- Obtain real external feedback through applications or technical conversations.

## Theory Side Sequence

Study theory just in time:

1. Vectors, matrices, and dot-product intuition.
2. Probability, logarithms, and information.
3. Training and validation sets, loss, gradients, and overfitting.
4. Neural networks and backpropagation.
5. PyTorch tensors and autograd.
6. Tokens, embeddings, and positional information.
7. Q, K, V and self-attention.
8. Transformer blocks, pretraining, instruction tuning, inference, decoding, and hallucination.

Train only small educational models and implement one simplified attention exercise. Do not make full-model training, CUDA, distributed training, RLHF, or complex paper reproduction part of the initial main line.

PyTorch reference: https://docs.pytorch.org/tutorials/beginner/basics/intro

## Deliberate Non-Goals for the Initial Route

- Learning several Agent frameworks simultaneously.
- Starting with multi-Agent orchestration.
- Training a large model from scratch.
- Collecting frameworks, vector databases, or papers without project evidence.
- Using framework abstractions without being able to explain the underlying loop.
