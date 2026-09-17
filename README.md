# Engineering Guardrails — The Engineering Contract

> AI coding assistants should not merely generate working code. They should operate within the engineering standards, architectural constraints, and delivery practices of the organisation.

A reusable `CLAUDE.md` baseline for teams building production software with Claude Code. Technology-neutral. Immediately adoptable. Designed for platform engineering teams.

---

## What this is

`CLAUDE.md` is loaded by Claude Code at the start of every session as persistent project context. Most teams use it for simple reminders — preferred tools, directory structure, linting commands. This repo goes further: it turns sound engineering and design practice into an **operational contract** between a team and its AI coding assistant.

The contract covers not just *what* to build, but *how* to reason about change, *when* to stop and escalate, and *what evidence* is required before calling something done.

---

## The five layers

Every `CLAUDE.md` in this repo is built from five layers:

| Layer | Purpose | Sections |
|---|---|---|
| 1. Engineering Principles | Mindset and design rules | §1–5 |
| 2. Guardrails | Non-negotiable runtime behaviour | §6–8 |
| 3. Delivery | How Claude works and deploys | §9 |
| 4. Operating Model | How Claude reasons and when it stops | §10–11 |
| 5. Completion Standard | What "done" means | §12 |

---

## Quick start

### Option A — Copy the core file
Copy `CLAUDE.md` into the root of your project. Fill in any `<placeholder>` values. Done.

### Option B — Start from a profile
Pick the profile that fits your context:

| Profile | When to use |
|---|---|
| [`adoption/lightweight-profile.md`](adoption/lightweight-profile.md) | Solo projects, small teams, getting started |
| [`adoption/standard-profile.md`](adoption/standard-profile.md) | Production software, professional teams |
| [`adoption/regulated-profile.md`](adoption/regulated-profile.md) | Compliance-sensitive: data governance, model risk, audit |

### Option C — Start from an example
Pick the example closest to your stack:

| Example | Stack |
|---|---|
| [`examples/agentic-application.CLAUDE.md`](examples/agentic-application.CLAUDE.md) | LangGraph, multi-agent, tool calling, memory |
| [`examples/llm-inference-service.CLAUDE.md`](examples/llm-inference-service.CLAUDE.md) | Model serving, prompt management, token budgets |
| [`examples/data-pipeline.CLAUDE.md`](examples/data-pipeline.CLAUDE.md) | Batch/streaming pipelines, data quality, lineage |
| [`examples/python-service.CLAUDE.md`](examples/python-service.CLAUDE.md) | FastAPI/Python, pytest, type hints, structlog |

Each example extends the core `CLAUDE.md` — copy both files into your project root, keeping the example content below the core.

---

## Supplementary rule modules

Domain-specific rules you can append to any profile:

| Module | When to add |
|---|---|
| [`rules/agentic-systems.md`](rules/agentic-systems.md) | Building agents, tool-calling systems, or multi-agent workflows |
| [`rules/data-engineering.md`](rules/data-engineering.md) | Data pipelines, schema management, quality gates, lineage |

---

## What this is not

`CLAUDE.md` is an advisory layer — it guides Claude's behaviour, not enforces it. For rules that must hold regardless of model judgment, use:

- **Claude Code hooks** — `PreToolUse` blocks for deterministic prevention
- **Pre-commit hooks** — linting, secret scanning, formatting
- **CI/CD gates** — test coverage, static analysis, dependency scanning
- **Branch protection + peer review** — organisational enforcement

The contract shapes engineering behaviour. The controls enforce it.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).
