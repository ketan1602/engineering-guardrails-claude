# Rules — Data Engineering

Supplementary rules for teams building data pipelines, ETL/ELT systems, and data platforms.
Append relevant sections to your `CLAUDE.md`. See also `examples/data-pipeline.CLAUDE.md` for a complete example.

---

## Idempotency

- Every pipeline run over the same input partition must produce identical output.
- Use upsert semantics (`INSERT ... ON CONFLICT DO UPDATE`) for all pipeline writes — never plain `INSERT`.
- Partition keys (date, batch ID, source ID) are explicit in every write. Never rely on insertion order.
- Re-running for a historical partition is safe by design, not convention.
- Test idempotency: run twice on the same input, assert outputs are byte-for-byte identical.

---

## Data Quality

- Validate at ingestion before any downstream write. Never write corrupt data.
- Required checks: null rate on required fields, row count within bounds, schema conformance.
- Quality rules are code — version-controlled, testable, not embedded in SQL comments.
- A quality failure halts the pipeline and raises an alert. It never continues with degraded data.
- Emit quality metrics (null rate, row count, schema drift detected) as structured logs on every run.

---

## Schema Management

- Schema changes are backward-compatible by default. Adding a nullable column is safe; renaming or removing is breaking.
- Breaking changes follow: add new → backfill → deprecate old → remove.
- Schemas are defined in code (Pydantic, dbt schema.yml, Avro, Protobuf) — never inferred from data at runtime.
- Schema version propagates in every message and write. Consumers handle mismatches explicitly.
- Never infer schema from a sample of production data. Inferred schemas drift silently.

---

## Lineage and Auditability

- Every output dataset records: source(s), pipeline version, run timestamp, input row count, output row count.
- A unique `run_id` links inputs to outputs on every pipeline run. This is the minimum lineage requirement.
- Column-level lineage is required for any field used in compliance, regulatory, or model training contexts.
- Deletions (including GDPR right-to-erasure) are audit-logged: who, when, which records, which datasets.
- Lineage metadata is co-located with data — not in a separate system that can drift out of sync.

---

## Error Handling

- A pipeline that partially succeeds is worse than one that fails cleanly.
- Use transactions where the target store supports them. Where not, implement compensating writes.
- Failed records go to a dead-letter store with: full record, error, pipeline version, run timestamp.
- Dead-letter stores are monitored. Unprocessed records older than the SLA trigger an alert.
- Abort the run if the error rate exceeds `PIPELINE_ERROR_THRESHOLD` (env var). Do not continue with degraded output.

---

## Backfill Patterns

- Backfills are re-runs over historical partitions — idempotency (above) makes them safe.
- Backfill scope is always explicit: start partition, end partition, dry-run flag.
- Run at reduced parallelism to avoid saturating source systems.
- Log per partition: started, completed, row count, duration.
- Never backfill into a table being actively written by the live pipeline without coordination.

---

## Configuration

- Source connections, target connections, and processing parameters are all env vars.
- Partition granularity (hourly, daily, monthly) is an env var — one pipeline codebase handles all granularities.
- Parallelism and batch size are env vars with documented defaults. No magic numbers in code.
- `DRY_RUN=true` must be supported: validate and process but write nothing to any target.
