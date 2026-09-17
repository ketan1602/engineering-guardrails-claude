# Rules — Agentic Systems

Supplementary rules for teams building agents, tool-calling systems, and multi-agent workflows.
Append relevant sections to your `CLAUDE.md`. See also `examples/agentic-application.CLAUDE.md` for a complete example.

---

## Infrastructure Component Matrix

Select components via env var. All implementations of the same role share an identical interface.

| Role | Open Source | Azure Native | AWS Native |
|---|---|---|---|
| Message Broker | RabbitMQ | Azure Service Bus | Amazon SQS / MQ |
| Cache | Redis | Azure Cache for Redis | ElastiCache for Redis |
| Container Registry | Harbor | ACR | ECR |
| Service Mesh | Istio | Azure Service Mesh (AKS) | AWS App Mesh |
| mTLS | Istio mTLS | Azure App Gateway (mTLS) | ACM + ALB mutual TLS |
| Relational DB | PostgreSQL | Azure Database for PostgreSQL | Amazon RDS for PostgreSQL |
| Graph DB | Neo4j | Azure Cosmos DB (Gremlin) | Amazon Neptune |
| Vector Store | Qdrant | Azure AI Search (vector) | Amazon OpenSearch (vector) |

**Env var convention:** `INFRA_<ROLE>=<value>` selects the implementation. Credentials are separate env vars.

---

## Agent Loop Rules

- Hard iteration ceiling on every loop (`MAX_ITERATIONS` env var). Never loop unbounded.
- Each iteration emits a structured log: agent name, step, tool called, input summary, latency, success/failure.
- Cycle detection: halt if the last N outputs are identical. Surface the stall; do not continue silently.
- Tool failures: retry with exponential backoff (max 3 attempts), then return a structured error to the caller.
- Final output is validated against a schema before leaving the agent boundary.

---

## Tool Registry Rules

- Tools are registered, not branched on — new tools add a file and a registry entry.
- Every tool declares: name, description, typed input schema, typed output schema.
- Tool implementations are stateless. All state lives in graph state or the backing store.
- Tool errors are returned as structured objects — the agent loop decides whether to retry or escalate.

---

## Memory Rules

- Short-term (within session): graph state object only. Never the local filesystem.
- Long-term (across sessions): backing store selected via env var (`INFRA_MEMORY_STORE`).
- Memory writes are idempotent — use a content hash or session ID as the deduplication key.
- Memory reads have a timeout and a fallback. A slow store must not block the agent loop.
- Never store raw PII in memory without explicit consent and a defined retention policy.

---

## Multi-Agent Rules

- Every agent has a declared role, a typed input contract, and a typed output contract.
- Agents communicate via message broker or shared state — never via direct cross-agent function calls.
- Orchestrator agents do not implement domain logic. They route, delegate, and aggregate only.
- Every inter-agent message carries the originating `trace_id`.
- Every blocking wait on another agent has a timeout and an escalation path.

---

## Observability Rules

- Emit a span per agent step: agent name, tool, tokens consumed, latency, outcome.
- Counters: `agent.steps.started`, `agent.steps.completed`, `agent.steps.failed`, `agent.tool_calls.<name>`.
- Alert when a loop exceeds 80% of `MAX_ITERATIONS` (warning) or 100% (error).
- Provide log structure sufficient to reconstruct any agent run from its `trace_id`.
