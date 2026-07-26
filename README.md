# Persona-Tiered Testing

> Match test investment to who traverses a path and what breaking it costs.

## What it does

A Claude Code skill that decides what tests a feature needs, which tests block deployment, and what goes to manual QA — using three persona litmus tests instead of intuition:

- **Critical path** — "I live abroad and my grandma needs to use this product — which path do I show her?" → one dedicated e2e-UI test, blocks deploy.
- **Technical path** — the path a tech-fluent user would traverse → unit tests for the logic + one API/integration test for the seam, blocks deploy.
- **Long tail / adversarial inputs** — "what if I enter 0 / -1? birth date in the future?" → devastating cases get blocking unit tests; the rest goes to manual QA, marked as `Manual-QA:` lines in the MR.

Plus the rules that keep it honest: an impact override for money-moving/data-destroying paths, a tiny-e2e discipline, and "recurring manual work is a smell — automate it."

## Install

```bash
npx skills add ishmum123/persona-tiered-testing
```

Works with Claude Code, Cursor, Codex, and any editor supported by the [skills CLI](https://skills.sh).

## Structure

```
skills/persona-tiered-testing/
  SKILL.md    tier table, adversarial-input dimension, rules, worked example, common mistakes
```

## Links

- Skills CLI: [skills.sh](https://skills.sh)
