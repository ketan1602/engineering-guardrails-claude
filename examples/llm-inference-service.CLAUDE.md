# Engineering Guardrails — LLM Inference Service

Extends the core `CLAUDE.md`. Copy both files into your project root; place this content below the core sections.

Applies to: model serving APIs, prompt orchestration layers, Bedrock/OpenAI/Azure OpenAI wrappers, AI Refinery integrations.

---

## L1. Model Selection

- Model ID, provider, and region are always env vars (`LLM_MODEL`, `LLM_PROVIDER`, `LLM_REGION`). Never hardcoded.
- Swapping model or provider requires no code change — all providers share an identical call interface (LSP).
- Log the model ID and provider on every inference request at `info` level.
- If a requested model is unavailable, fail loudly with the model name in the error — never silently fall back to a different model.

---

## L2. Prompt Management

- Prompts are versioned artifacts — stored in files, reviewed like code, tested like functions.
- System prompts live in `prompts/` as named, versioned files (e.g. `prompts/classification_v3.txt`).
- No inline f-string prompt construction in business logic. Use a dedicated template renderer with named, typed variables.
- Prompt version is logged on every inference call so responses can be traced to the prompt that produced them.
- A prompt change requires a test asserting the expected output structure before merging.

---

## L3. Token and Cost Management

- Max input tokens and max output tokens are env vars with documented defaults. No magic numbers in code.
- Truncate inputs that exceed the context window; log a warning with the original and truncated token counts.
- Log tokens consumed (input, output, total) on every call at `info` level — this is the signal for cost attribution.
- Emit a counter `llm.tokens.input` and `llm.tokens.output` per model per request for cost dashboards.
- Never pass unbounded user input directly to a model. Always validate and truncate at the boundary.

---

## L4. Retry and Resilience

- Transient errors (rate limit, timeout, 5xx) are retried with exponential backoff: base 1 s, max 3 attempts, jitter.
- Non-transient errors (invalid request, auth failure, context limit exceeded) are never retried — fail immediately with a structured error.
- Every retry attempt is logged with attempt number, error type, and delay.
- Circuit breaker: if the error rate for a provider exceeds `LLM_ERROR_THRESHOLD` (env var) in a rolling window, stop routing to that provider and alert.
- Timeout is always explicit (`LLM_TIMEOUT_SECONDS` env var). Never rely on the SDK's default timeout.

---

## L5. Response Validation

- Never pass raw model output to a downstream caller. Always parse and validate against a schema first.
- If the model returns output that does not match the expected schema, log the raw output at `debug` and return a structured error — never a partial or malformed response.
- Structured outputs (JSON mode, tool calls) are validated with the same schema on every call, not just during development.
- Content safety: apply output filtering appropriate to the deployment context before returning to the caller.

---

## L6. Security and Privacy

- Never log prompt content or model responses at `info` level. Use `debug` only, and ensure `debug` is disabled in production.
- Strip PII from inputs before sending to any external model provider. Log a counter when stripping occurs.
- API keys for model providers are injected via secret manager — never in env files committed to source control.
- Track which model version processed which request for audit and compliance purposes.
- Multi-tenant services must namespace prompt logs and token counters by tenant ID — never aggregate across tenants.
