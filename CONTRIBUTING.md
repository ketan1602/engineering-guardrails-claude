# Contributing

## What makes a good contribution

- **A new example** — a complete `CLAUDE.md` for a specific stack or domain that isn't covered yet. Must be written from real project experience, not assembled from first principles. Thin, generic examples will be declined.
- **An improvement to the core** — a rule that is missing, redundant, or imprecise. Open an issue first explaining the problem before submitting a PR.
- **A new rules module** — a supplementary rule set for a domain (e.g. `rules/security.md`, `rules/ml-training.md`). Scope it tightly; it should cover rules that don't belong in the core.
- **A correction** — wrong, outdated, or misleading guidance. PRs welcome without prior issue.

## What we won't merge

- Rules that Claude already follows reliably by default — if a rule doesn't change behaviour, it adds noise without value.
- Technology-specific rules in the core `CLAUDE.md` — those belong in `examples/`.
- Rules written as rationale rather than instructions — `CLAUDE.md` content must be imperative and Claude-facing, not explanatory.
- Examples from stacks you haven't used in production.

## Format

Follow the register of the existing files exactly:
- Imperative sentences, no rationale
- Bullet lists for rules, numbered lists for sequences
- No multi-paragraph explanations
- Section headers consistent with the core file's style

## Submitting

1. Fork the repo
2. Create a branch: `git checkout -b add/go-service-example`
3. Make your changes
4. Open a PR with a one-paragraph description of what the contribution adds and what experience it is drawn from
