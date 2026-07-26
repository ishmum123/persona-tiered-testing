---
name: persona-tiered-testing
description: Use when deciding what tests a feature or change needs, which tests should block deployment, how much testing is enough, or where manual QA fits — for product features in a deployed application
---

# Persona-Tiered Testing

## Overview

Match test investment to who traverses a path and what breaking it costs. Classify every path with the persona litmus tests below; each tier maps to a coverage type and a deploy gate. Prevents both under-testing the money path and over-testing the long tail. The litmus tests ARE the cut line — never cut by intuition.

## Classify the path

| Tier | Litmus test | Coverage | Deploy gate |
|---|---|---|---|
| 1. Critical path | "I live abroad and my grandma needs to use this product — which path do I show her?" — the ONE journey you'd demo (eCommerce: search → product → cart → checkout). Most features are NOT on it, even features every customer uses | Dedicated e2e-UI test | Blocks |
| 2. Technical path | "The path a gen-z fluent with non-default FB/Insta settings would traverse" | Unit tests for the logic + one API/integration test for the seam | Blocks |
| 3. Long-tail | Only a CSE grad poking at it: obscure paths, adversarial inputs — "what if I enter 0 / -1? birth date in the future?" | Devastating/high-impact cases: unit tests (blocking). Rest: manual QA, marked in the MR | Unit part blocks |

## Adversarial inputs are a dimension, not just tier 3

Input edge cases exist on every path — grandma typos her phone number on the critical path too. Apply the tier-3 litmus wherever input enters, on any tier. Route each case: devastating (money, data loss, security) → blocking unit test; rest → marked manual QA.

## Rules

1. **Impact override.** A path that moves money or destroys data gets automated blocking coverage regardless of persona. Admin refund tool = CSE-grad traffic, tier-1 impact.
2. **Blocking e2e stays tiny** — grandma's path only. A flaky blocking e2e halts all deploys: fix or demote same day.
3. **Unit tests don't verify wiring.** A path can regress with every unit green. Each tier-2 seam gets one API-level test hitting the real endpoint.
4. **Manual-QA marking convention.** Every MR lists its unautomated edge cases under a `Manual-QA:` section, one case per line — greppable, checkable by QA. Unmarked = untested.
5. **Recurring manual work is a smell.** A manual check repeated every deploy is a tier-1/2 case in disguise — automate it.

## Worked example

Feature: change delivery address before shipment (eCommerce).
- Grandma test: she buys books, rarely edits an in-flight order → not the critical path → no new blocking e2e; existing purchase e2e untouched.
- Gen-z test: yes → tier 2: unit tests for the eligibility gate (shipped/cancelled → reject, courier hook not fired) and address validation; one API test proving the endpoint updates the DB and fires the courier hook once.
- Adversarial: address change racing the shipped transition → wrong delivery, costs money → devastating → blocking test. Out-of-zone address, double-click double-submit → `Manual-QA:` lines in the MR.

## Common mistakes

- Cutting by intuition instead of running the litmus tests — the rationale becomes unfalsifiable.
- Inflating tier 1 to "any path normal users touch" — that grows the blocking e2e suite until it's slow and flaky. Tier 1 is the single demo journey.
- Prescribing a manual click-through "every deploy" instead of writing the e2e once.
- Treating tier 3 as a place: edge cases live on tier-1 paths too.
- Letting manual-QA cases float in reviewers' heads instead of `Manual-QA:` lines in the MR.
