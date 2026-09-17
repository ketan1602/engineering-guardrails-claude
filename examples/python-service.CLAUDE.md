# Engineering Guardrails — Python Service

Extends the core `CLAUDE.md`. Copy both files into your project root; place this content below the core sections.

Applies to: FastAPI services, Python microservices, REST APIs, async Python applications.

---

## P1. Type Annotations

- All function signatures must be fully annotated: parameters and return types.
- No bare `Any` without a comment explaining why it cannot be typed more precisely.
- Use `TypedDict` or `dataclasses` for structured data — never untyped `dict` at module boundaries.
- Run `mypy --strict` (or equivalent) as part of CI. Type errors are blocking.
- Generic types must be parameterised: `list[str]` not `list`, `dict[str, int]` not `dict`.

---

## P2. Testing Conventions

- Test files mirror source structure: `src/services/user.py` → `tests/services/test_user.py`.
- Test names describe behaviour: `test_returns_404_when_user_not_found`, not `test_get_user`.
- Mock only at system boundaries (external HTTP, databases, message brokers) — never mock internal functions.
- Integration tests use real infrastructure via `docker compose` or `testcontainers`. No in-memory fakes for external stores.
- Every public function has at least one test for the happy path and one for the primary failure mode.
- Use `pytest-asyncio` for async test functions. Never mix sync and async in the same test module.

---

## P3. Logging

- Use `structlog` for all logging. No `logging.getLogger` or bare `print()`.
- Bind request-scoped context (request ID, user ID, trace ID) at the middleware layer — not in individual functions.
- Log at `info` for normal operations, `warning` for degraded paths, `error` for failures.
- Never log request or response bodies at `info` — use `debug` and ensure it is disabled in production.

---

## P4. FastAPI Conventions

- One router per domain concern, one file per router, all mounted in `app/main.py`.
- Request and response models are `pydantic.BaseModel` subclasses — never raw `dict`.
- Use `Depends()` for all shared dependencies (DB sessions, auth, config). Never instantiate dependencies inside route handlers.
- Startup validation: verify all required env vars and backing-service connections on `lifespan` startup. Fail loud if any are absent.
- Route paths use snake_case for path parameters: `/users/{user_id}`, not `/users/{userId}`.
- Specific paths before parameterised paths in every router: `/users/me` before `/users/{user_id}`.

---

## P5. Dependency Management

- Pin all direct dependencies to exact versions in `requirements.txt` or `pyproject.toml`.
- Separate `requirements.txt` and `requirements-dev.txt` (or `[dev]` extras). Dev tools never go to production.
- Never use `pip install` in a Dockerfile without pinned versions.
- Dependency updates are a deliberate action — not a side effect of running `pip install -r requirements.txt` without a lockfile.

---

## P6. Async Patterns

- Use `async def` for all route handlers and any function that awaits I/O.
- Never call blocking I/O (file reads, sync DB calls, `requests`) from an async context. Use `asyncio.to_thread` if unavoidable.
- Async context managers for resources with explicit open/close semantics (DB sessions, HTTP clients).
- Background tasks use `asyncio.create_task` or the framework's task queue — never `threading.Thread` in an async application.
