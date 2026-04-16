# dalton-skills

Skill library for AI agents operating the [Dalton](https://getdalton.com) platform. Each skill is a Markdown file that tells an agent what to do, in what order, and when to hand off to a sibling skill.

## Directory structure

```
skills/
├── dalton-core/           # Product knowledge, install, onboarding, goals, split tests, troubleshooting
│   ├── SKILL.md           # Router — start here for any core question
│   ├── dalton-info/       # What Dalton is, how it works, pricing, fit
│   ├── dalton-script-install/  # Snippet install across all platforms
│   ├── dalton-setup/      # End-to-end onboarding orchestrator
│   ├── dalton-page-goals/ # Conversion goal selection and configuration
│   ├── dalton-split-tests/# URL split tests (full-page comparisons)
│   └── dalton-help/       # Troubleshooting + support escalation
│
├── dalton-editor/         # Visual Editor — create, edit, copy experiments
│   ├── SKILL.md           # Router + shared editor concepts
│   ├── creating-experiments/
│   ├── editing-experiments/
│   └── copying-experiments/
│
├── dalton-integrations/   # External platform connections
│   ├── SKILL.md           # Router + shared ad-personalization concepts
│   ├── google-analytics/
│   ├── google-ads/
│   └── meta-ads/
│
├── dalton-data/           # Experiment data, results, privacy
│   ├── SKILL.md           # Router
│   ├── results-interpretation/
│   └── privacy-and-storage/
│
└── references/            # Shared reference docs (not skills)
    ├── platform-urls.md   # Live URL/UI map, verified 2026-04-16
    └── prompting-best-practices.md
```

Source of truth for product facts: `~/Documents/dalton/dalton-docs/`.

## Working agreement

- **Audience:** primarily Dalton customers (marketing / CRO), secondarily internal team.
- **Operator-mode default:** if the consuming agent has browser/filesystem tools, skills instruct it to *do* the task on the user's behalf (via `platform.getdalton.com`). Manual walkthrough is the fallback.
- **Permission etiquette:** state → confirm → execute → report. Never launch experiments or push live changes without explicit user confirmation.
- **Support escalation:** Intercom on `platform.getdalton.com` is the default. Dedicated Slack/Teams channel is a conditional fallback (not all customers have one).
- **Skill descriptions** are written to be "pushy" on when to trigger and explicit about when NOT to, so sibling skills don't overlap.
- **Iteration style:** one skill family at a time; vibe-based review (no eval harness).

## Goals

1. An agent **with** browser access should be able to use Dalton like an experienced Dalton user — navigating the platform, creating and launching experiments, and completing tasks on the user's behalf.
2. An agent **without** browser access should be able to confidently and correctly help users handle Dalton — giving accurate guidance, correct URLs, and the right sequence of steps.

## Adding a new skill

1. Create `skills/<family>/<skill-name>/SKILL.md`.
2. Add frontmatter: `name`, `description` (written as a triggering signal — when to use AND when not to).
3. Follow the operator-mode pattern: default path assumes browser tools; manual walkthrough is the fallback.
4. Add routing rows to the family's `SKILL.md` router.
5. Update `to-do.md`.
