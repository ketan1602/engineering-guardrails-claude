# Adoption Profile — Lightweight

For solo projects, small teams, hackathons, and getting started quickly.

Copy the sections below into a `CLAUDE.md` at the root of your project. This is the minimum viable contract — five core sections plus the three operating model sections.

---

## When to upgrade to Standard

Move to the standard profile when:
- More than one person is committing to the repo
- The project has users outside the team
- You are running the service in a shared or production environment
- Secrets need to be managed beyond a local `.env` file

---

## Sections to include

From the core `CLAUDE.md`, include:

| Section | Why |
|---|---|
| §1 Functionality Preservation | Prevents breaking changes during refactors |
| §2 File Size | Keeps files navigable as the project grows |
| §3 SOLID Principles | Core design discipline |
| §4 12-Factor App | Config and observability hygiene from day one |
| §5 Simplicity | Prevents over-engineering |
| §10 Change Workflow | How Claude should approach any task |
| §11 Escalation Conditions | When Claude should stop and ask |
| §12 Definition of Done | What complete actually means |

Sections §6–§9 (Error Handling, Secrets Management, Config-Driven Infrastructure, Deploy Workflow) can be added when the project reaches the point where they matter — typically when you have more than one environment or a CI/CD pipeline.

---

## Minimum `CLAUDE.md` for a lightweight project

```
# Engineering Guardrails

## 1. Functionality Preservation
[copy from core]

## 2. File Size — hard limit 150 lines
[copy from core]

## 3. SOLID Principles
[copy from core]

## 4. 12-Factor App
[copy from core]

## 5. Simplicity — YAGNI, KISS, DRY
[copy from core]

## 6. Change Workflow
[copy §10 from core]

## 7. Escalation Conditions
[copy §11 from core]

## 8. Definition of Done
[copy §12 from core]
```
