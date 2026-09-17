# Adoption Profile — Regulated

For compliance-sensitive environments: data governance, model risk, financial services, healthcare, public sector.

Start with the full standard profile, then add the sections below to your `CLAUDE.md`.

---

## R1. Data Governance

- Every dataset has a declared owner, classification (public / internal / confidential / restricted), and retention policy.
- Data classification is enforced in code — confidential and restricted data never flows to a lower-classification store.
- PII fields are declared in the schema and tagged. Any function that reads a PII field is treated as a boundary requiring explicit validation.
- Data access is logged: who accessed what dataset, when, for what purpose (purpose must be in the request context).
- Deletion requests (GDPR right-to-erasure) are tracked end-to-end: request received → records identified → records deleted → confirmation logged.

---

## R2. Model Governance

- Every model deployment records: model ID, version, provider, training data lineage, evaluation results, approval status.
- Model outputs that affect regulated decisions (credit, hiring, healthcare triage, benefits) are logged with the input, output, model version, and timestamp.
- Model updates require a documented evaluation against a defined test set before deployment to production.
- A/B tests and shadow deployments must have a defined end date and evaluation criterion — no indefinite experiments.
- Bias monitoring metrics are defined before deployment and evaluated on a documented schedule.

---

## R3. Audit Logging

- Audit logs are append-only and tamper-evident. No update or delete operations on the audit log store.
- Every audit log entry includes: actor (user or service), action, resource, outcome, timestamp, `trace_id`.
- Audit logs are retained for the period required by the applicable regulation (minimum 7 years for financial services).
- Audit log writes are synchronous — a failed audit write must fail the originating operation, not be silently skipped.
- Audit logs are stored separately from application logs and are accessible only to authorised roles.

---

## R4. AI-Generated Code Controls

- AI-generated code changes are reviewed by a human before merging to the main branch. No auto-merge of AI-generated PRs.
- AI-generated code that modifies security controls, authentication, authorization, or data access must be reviewed by a security-competent reviewer, not just any team member.
- The commit message or PR description must identify AI-assisted sections so reviewers know where to focus scrutiny.
- AI-generated migrations, schema changes, and infrastructure changes require a separate approval step before execution.

---

## R5. Privacy by Design

- PII is never logged at any level — not even at DEBUG.
- PII is never stored in a cache, message queue, or event stream without encryption and a defined expiry.
- Data minimisation: collect and retain only the fields necessary for the stated purpose. Justify every field.
- Cross-border data transfers are explicitly approved and implemented via the correct legal mechanism (SCCs, adequacy decision, etc.).
- Privacy impact assessments are required before deploying any new feature that processes personal data.

---

## R6. Escalation Additions (append to §11)

In addition to the standard escalation conditions, also stop and present options when:

- The change processes, stores, or transmits personal data in a new way.
- The change affects a model that produces regulated outputs.
- The change modifies audit logging behaviour or coverage.
- The change affects data retention or deletion logic.
- A compliance or legal review has not yet been completed for the feature.
