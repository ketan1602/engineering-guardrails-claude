# Adoption Profile — Standard

For professional teams building production software.

Use the core `CLAUDE.md` in full. This profile adds guidance on how to extend it for your team.

---

## What you get out of the box

The core `CLAUDE.md` covers all twelve sections:

| Layer | Sections | Covers |
|---|---|---|
| Engineering Principles | §1–5 | Design, structure, simplicity |
| Guardrails | §6–8 | Error handling, secrets, infrastructure |
| Delivery | §9 | Deployment workflow |
| Operating Model | §10–11 | How Claude works, when it stops |
| Completion Standard | §12 | What done means |

---

## Recommended additions for most teams

**Add a stack-specific example file.** Choose the closest match from `examples/` and append it to your `CLAUDE.md`. This gives Claude concrete patterns for your technology choices.

**Add relevant rules modules.** If you are building agents, append `rules/agentic-systems.md`. If you are building pipelines, append `rules/data-engineering.md`.

**Fill in your team's specifics.** The core file has placeholders for:
- Your private registry hostname
- Your default Kubernetes context
- Your secret manager of choice
- Your CI/CD tooling

---

## Typical `CLAUDE.md` for a standard team

```
[core CLAUDE.md — all 12 sections, placeholders filled in]

---

[chosen example file — e.g. python-service or agentic-application]

---

[chosen rules module(s) if applicable]
```

---

## When to upgrade to Regulated

Move to the regulated profile when:
- The service processes personal data subject to GDPR, CCPA, or equivalent regulation
- The system includes AI models whose outputs affect regulated decisions (credit, hiring, healthcare)
- Your organisation requires audit trails for AI-generated code
- You operate in financial services, healthcare, or public sector
