---
name: dalton-data
description: Answer questions about Dalton's experiment data — interpreting results, understanding the learning phase and certainty thresholds, or explaining what Dalton collects, where it stores data, and how it handles privacy. Routes to sub-skills: dalton-results-interpretation (reading the dashboard, when to trust results, when to act) and dalton-privacy-and-storage (EU data storage, cookies, GDPR, what Dalton does and doesn't collect). Use this skill whenever someone asks about results, significance, lift, certainty, conversions, the learning phase, what the dashboard numbers mean, data storage, privacy, GDPR, PII, or cookies.
---

# Dalton data (family root)

Router for the data family. Two distinct areas:

| User is asking about | Use |
|---|---|
| Reading the results dashboard, whether to trust a variant's numbers, what lift / certainty / learning phase mean, when to pause, when to declare a winner | `dalton-results-interpretation` |
| What data Dalton collects, where it's stored (EU / eu-central), GDPR / CCPA stance, cookies set, whether PII is involved, compliance questions | `dalton-privacy-and-storage` |

Both are knowledge-heavy skills. `dalton-results-interpretation` has an operator angle (read the dashboard for the user); `dalton-privacy-and-storage` is mostly about giving accurate, confident answers to privacy/compliance questions without making things up.

## When results interpretation is really a troubleshooting question

If the user's "results question" is really "my dashboard shows zero sessions", or "numbers don't match GA4", or "results have been stuck at 50/50 for a month" — route to `dalton-help`. `dalton-results-interpretation` is for "my data is flowing, help me read it." If data isn't flowing, that's a different problem.

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `optimization/results-dashboard.md` — dashboard walkthrough + learning phase
- `optimization/how-the-algorithm-works.md` — MAB phases, Thompson Sampling
- `faq/results.md` — results FAQ
- `data/storage.md` — EU storage
- `data/cookies.md` — cookie details
- `technical/privacy-security.md` — privacy overview
