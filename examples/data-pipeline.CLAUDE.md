# Engineering Guardrails — Data Pipeline

Extends the core `CLAUDE.md`. Copy both files into your project root; place this content below the core sections.

Applies to: batch pipelines, streaming pipelines, ETL/ELT, dbt transformations, Spark jobs, Airflow DAGs.

---

## D1. Idempotency

- Every pipeline run for the same input partition must produce identical output. No exceptions.
- Use `INSERT ... ON CONFLICT DO UPDATE` (upsert) or equivalent — never plain `INSERT` for pipeline outputs.
- Partition keys (date, batch ID, source ID) must be explicit in every write. Never rely on insertion order.
- Re-running a pipeline for a historical partition must be safe by design, not by convention.
- Test idempotency explicitly: run the pipeline twice on the same input; assert outputs are identical.

---

## D2. Data Quality Gates

- Validate data at ingestion before writing to any downstream store. Fail fast; never write corrupt data.
- Quality rules are code: version-controlled, testable, not embedded in SQL comments or documentation.
- Required checks for every dataset: null rate on required fields, row count within expected bounds, schema conformance.
- A quality failure halts the pipeline and raises an alert — it never silently continues with degraded data.
- Quality metrics (null rate, row count, schema drift) are emitted as structured logs and counters on every run.

---

## D3. Schema Management

- Schema changes are backward-compatible by default. Adding a nullable column is safe; renaming or removing is a breaking change.
- Breaking schema changes require a migration plan: add the new column, backfill, deprecate the old column, then remove.
- Schemas are defined in code (e.g. Pydantic, dbt schema.yml, Avro/Protobuf), not inferred from data at runtime.
- Schema version is propagated in every message and write — consumers must handle version mismatches explicitly.
- Never infer schema from a sample of production data. Inferred schemas drift silently.

---

## D4. Backfill Patterns

- Backfills are re-runs of the pipeline over historical partitions — they must be safe by D1 (idempotency).
- Backfill scope is always explicit: start partition, end partition, dry-run flag.
- Run backfills at reduced parallelism to avoid saturating source systems.
- Backfill progress is logged per partition: started, completed, row count, duration.
- Never backfill into a table that is being actively written to by the live pipeline without coordination.

---

## D5. Lineage and Auditability

- Every output dataset records: source dataset(s), pipeline version, run timestamp, input row count, output row count.
- Pipeline runs are logged with a unique `run_id` that links inputs to outputs. This is the minimum lineage requirement.
- Column-level lineage is required for any field used in a compliance, regulatory, or model training context.
- Deletions (including GDPR right-to-erasure) are audit-logged: who requested, when, which records, which datasets affected.
- Lineage metadata is written to the same store as data — not a separate system that can drift out of sync.

---

## D6. Error Handling for Data Failures

- A pipeline that partially succeeds is worse than one that fails cleanly — partial writes leave the store in an unknown state.
- Use transactions where the target store supports them. Where not, implement compensating writes.
- Failed records go to a dead-letter store with the full record, the error, the pipeline version, and the run timestamp.
- Dead-letter stores are monitored. Unprocessed records older than the SLA threshold trigger an alert.
- Circuit breaker: if the error rate for a run exceeds `PIPELINE_ERROR_THRESHOLD` (env var), abort the run rather than continuing.

---

## D7. Pipeline Configuration

- Source connections, target connections, and processing parameters are all env vars. No hardcoded endpoints.
- Partition granularity (hourly, daily, monthly) is an env var — the same pipeline code handles all granularities.
- Parallelism and batch size are env vars with documented defaults. Never magic numbers in code.
- Dry-run mode (`DRY_RUN=true`) must be supported: process and validate, but do not write to any target store.
