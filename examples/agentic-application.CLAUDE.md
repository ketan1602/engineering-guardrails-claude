# Engineering Guardrails — Agentic Application

Extends the core `CLAUDE.md`. Copy both files into your project root; place this content below the core sections.

Applies to: LangGraph agents, multi-agent workflows, tool-calling systems, memory-augmented applications.

---

## A1. Agent Loop Architecture

- Every agent loop must have a hard iteration ceiling (`max_iterations` env var, default ≤ 25). Never loop unbounded.
- Each iteration must emit a structured log entry: agent name, step number, tool called, input summary, output summary, latency.
- Detect and break cycles: if the last N outputs are identical, halt and surface the stall rather than continuing.
- Tool call failures are not loop-terminating by default — retry with exponential backoff (max 3 attempts), then surface the failure to the caller.
- Final agent output must be validated against a schema before returning to the caller. Never pass raw LLM completions through.

---

## A2. Tool Calling Conventions

- Tools are registered, not branched on. New tools add a file and a registry entry — never an `elif` in the dispatch function.
- Every tool has: a name, a one-line description, a typed input schema, a typed output schema.
- Tool implementations are stateless. State lives in the graph state object or the backing store, never in the tool.
- Tools that call external APIs must respect rate limits, implement retry with backoff, and emit latency metrics.
- Tool errors must be returned as structured error objects, not raised exceptions — the agent loop decides whether to retry or escalate.

---

## A3. Model Selection

- Model is always selected via env var (`LLM_MODEL`, `LLM_PROVIDER`). Never hardcoded.
- Temperature, max tokens, and other generation parameters are env vars. No magic numbers in code.
- Swapping model or provider means changing config, not code — all providers share an identical call interface.
- Log the model name and provider on every inference call.

---

## A4. Prompt Management

- Prompts are code — version-controlled, reviewed, and tested like any other artifact.
- System prompts live in versioned files (e.g. `prompts/system_v2.txt`), not inline strings.
- No f-string prompt construction in business logic. Use a dedicated template renderer with named variables.
- Prompt changes require a corresponding test that asserts the expected output shape.

---

## A5. Memory Architecture

- Short-term memory (within a session): graph state object. Never the local filesystem.
- Long-term memory (across sessions): backing store (vector DB, relational DB, graph DB) — selected via env var.
- Episodic memory writes are idempotent. Use a content hash or session ID as the deduplication key.
- Memory reads must have a timeout and a fallback — a slow memory store must not block the agent loop.
- Never store raw PII in memory without explicit consent and a retention policy.

---

## A6. Multi-Agent Coordination

- Agent roles are declared, not inferred. Each agent has a named role, a defined input contract, and a defined output contract.
- Agents communicate via the message broker or shared state — never via direct function calls across agent boundaries.
- Orchestrator agents do not implement domain logic. They route, delegate, and aggregate.
- Every inter-agent message carries the originating `trace_id` so the full call graph is traceable.
- Deadlock prevention: every blocking wait on another agent has a timeout and an escalation path.

---

## A7. Agentic Observability

- Emit a span for every agent step: agent name, tool called, tokens consumed, latency, success/failure.
- Emit counters: `agent.steps.started`, `agent.steps.completed`, `agent.steps.failed`, `agent.tool_calls.<name>`.
- Log the full tool input and output at DEBUG level — never at INFO (too noisy in production).
- Alert threshold: agent loop exceeding 80% of `max_iterations` is a warning signal; at 100% it is an error.
- Provide a replay endpoint or log structure that allows any agent run to be reconstructed from its trace.
