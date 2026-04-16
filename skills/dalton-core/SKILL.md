---
name: dalton-core
description: Entry point for foundational Dalton questions and operations — what Dalton is, how to install the script, how to onboard a new account, how to configure conversion goals, how to set up URL split tests, and how to troubleshoot problems. Use this skill as the starting point for any Dalton question that isn't about the visual editor, integrations, or interpreting data. Routes to the right sub-skill: dalton-info (product knowledge), dalton-script-install (snippet install), dalton-setup (full onboarding sequence), dalton-page-goals (conversion goals), dalton-split-tests (full-page URL tests), dalton-help (troubleshooting + support escalation).
---

# Dalton core (family root)

Router for the core skill family. Covers everything from "what is this product" through first-time onboarding to ongoing maintenance tasks. If the user is in the visual editor creating variants, route to `dalton-editor`. If they're wiring up analytics or ads, route to `dalton-integrations`. If they're reading results data, route to `dalton-data`.

## Which sub-skill to use

| User is asking / doing | Use |
|---|---|
| "What is Dalton?", "How does it work?", "Is it right for us?", "How does it compare to A/B testing?", "What's a multi-armed bandit?", "How much traffic do I need?", pricing / fit questions | `dalton-info` |
| "How do I install the script?", "Where does the snippet go?", "Installation required" banner, platform-specific install (Shopify, Webflow, React, Next.js, GTM, etc.), sharing snippet with a developer, verifying the script loads | `dalton-script-install` |
| "Walk me through getting started", "How do I launch my first experiment?", "I just signed up", full onboarding from install → goal → first experiment → launch | `dalton-setup` |
| "What should I optimize for?", "How do I set the conversion goal?", "How do I track a form submit / signup / purchase?", setting or changing the goal on a page, secondary metrics, verifying the goal fires | `dalton-page-goals` |
| "How do I test two completely different pages?", URL split tests, full-page redesign comparison, choosing between a split test and a regular experiment | `dalton-split-tests` |
| Something is broken or not working — script not detected, variants not showing, dashboard stuck at zero, prompt errors, "how do I contact Dalton" | `dalton-help` |

## Family scope

`dalton-core` owns the **foundational layer**: product knowledge, installation, onboarding, and the configuration steps (goals, split-test setup) that must be in place before the editor or integrations are useful. It does not own variant creation (→ `dalton-editor`), analytics/ad wiring (→ `dalton-integrations`), or data interpretation (→ `dalton-data`).

When a user request spans this boundary — e.g., "set up my goal and then create my first experiment" — handle the core phase first (`dalton-page-goals`), confirm it's done, then hand off to `creating-experiments`.

## When to hand off to another family

- User wants to create, edit, or copy variants in the visual editor → `dalton-editor`
- User wants to connect GA4, Google Ads, or Meta Ads → `dalton-integrations`
- User wants to read or interpret experiment results → `dalton-data`

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `getting-started/` — install, first experiment, onboarding
- `faq/setup.md`, `faq/troubleshooting.md` — common questions and fixes
- `optimization/results-dashboard.md` — referenced by `dalton-setup` for post-launch expectations
