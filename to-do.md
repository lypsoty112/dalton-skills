# Dalton skills — to-do

Tracks each skill to write. Source of truth for product facts: `~/Documents/dalton/dalton-docs/`.

Legend: `[ ]` todo · `[~]` in progress · `[x]` done

## dalton-core

- [x] **dalton-info** — explain what Dalton is, how it works, who it's for, MAB vs A/B, pricing, fit. Routes operational questions to other skills.
- [x] **dalton-help** — triage → fix → escalate. Browser-agent verification; Intercom default, Slack/Teams conditional fallback.
- [x] **dalton-script-install** — operator-mode: fetch snippet from platform.getdalton.com, apply it in site-builder or repo, verify on live page. Manual walkthrough is the fallback.
- [x] **dalton-setup** — end-to-end onboarding orchestrator (install → goal → first experiment → pre-launch QA → launch). Owns the sequence; delegates each phase.
- [x] **dalton-page-goals** — four goal types + GA4 events; pick the most-downstream measurable action; verify goal fires before launch.
- [x] **dalton-split-tests** — URL split test (two+ completely different pages). Three traffic modes (Fixed / Smart / Aggressive). When to use vs regular experiment.

## dalton-editor

- [x] **dalton-editor/SKILL.md** — router + shared concepts (editor URL, Insights, templates, Brand Intelligence, prompting rules, can/can't test).
- [x] **creating-experiments** — three entry points (Insight / template / custom prompt); full prompting guide; Brand Intelligence; preview discipline.
- [x] **editing-experiments** — iteration loop; re-prompt discipline; undo-vs-delete-vs-re-prompt; common iteration traps.
- [x] **copying-experiments** — multi-page experiments with wildcards + the "elements must be identical across pages" rule. Flags that one-off UI copy between pages doesn't exist yet.

## dalton-integrations

- [x] **dalton-integrations/SKILL.md** — router + shared ad-personalization concepts.
- [x] **google-analytics** — connect GA4; GA4 events as goals; Optimized-vs-Control audiences; holdout split (20/80 default).
- [x] **google-ads** — account-level tracking template; OAuth; pick personalization level (campaign / ad group / creative); AI groups ads; generate + approve variants. Beta.
- [x] **meta-ads** — per-ad URL parameters (Meta has no account-level template); OAuth; personalization level (campaign / ad set / ad); bulk-edit tactics. Beta.

## dalton-data

- [x] **dalton-data/SKILL.md** — router between results-interpretation and privacy-and-storage.
- [x] **results-interpretation** — reading the dashboard, the three algorithm phases, learning-phase thresholds (~250 sessions + ~100 conversions), 95%+ certainty rule, when to act vs wait vs pause.
- [x] **privacy-and-storage** — EU data storage (eu-central), what's collected and what isn't (no PII), the two cookies (`dalton_session_[id]`, `dalton_device`, 100-day expiration, first-party), GDPR stance, scripted answers to common legal questions.
- *(deferred)* **data-export** — pulling raw experiment data. Confirmed deferred; depends on Tinybird/CSV export capabilities.

## References (shared content)

- [x] **references/prompting-best-practices.md** — golden prompting rules, recipe for vague re-prompts, "when prompts repeatedly fail" fallback. Linked from `creating-experiments`, `editing-experiments`, and `dalton-help`.

## Skill review — iteration 2 (applied)

- [x] **Cold-start Phase 0** added to `dalton-setup` — how to discover `customer_id` and `page_id` from observed URLs without inventing paths.
- [x] **Prompting rules extracted** to shared `references/prompting-best-practices.md`. `creating-experiments` and `editing-experiments` trimmed to summary + link.
- [x] **Description overlaps tightened** — GA4-goal boundary between `google-analytics` and `dalton-page-goals`; sequence vs. single-phase boundary between `dalton-setup` and `creating-experiments`; data-flow vs. interpretation boundary between `dalton-help` and `results-interpretation`.

## Skill review — iteration 3 (applied, browser-verified)

Verified against the live platform on 2026-04-16 by driving `platform.getdalton.com` directly. Resulting changes:

- [x] **`references/platform-urls.md`** — full URL / UI map, replaces placeholder references.
- [x] **`dalton-script-install`** rewritten — snippet lives on the Integrations page (not Settings), platform-tab list trimmed to what's actually offered (JavaScript, Shopify, Webflow, Wix, Elementor, React, Next.js, NuxtJS, Framer), added Antiflickering / Reapply Changes toggles, stronger GTM warning, real CDN domains (`cdn.getdalton.com`, `track.getdalton.com`), per-page install verification via Page Settings gear.
- [x] **`dalton-split-tests`** rewritten — confirmed URL `/admin/[customer_id]/traffic-split` (and `/new` for create), 4-step wizard (Name → URLs → Conversion Goal → Traffic Split).
- [x] **`dalton-page-goals`** rewritten — configuration via Page Settings gear panel; **Secondary Metrics is live** (UI present, not roadmap-only); inline goal edit via page header link.
- [x] **`dalton-editor/SKILL.md`** rewritten — Visual Editor UI mapped (prompt box placeholder "What would you like to test?", Actions button, Add context element button, experiment statuses Live/Paused/Draft).
- [x] **`creating-experiments`, `editing-experiments`, `copying-experiments`** — editor URL references now point to the platform-urls reference; prompt-box placeholder text corrected.
- [x] **`dalton-integrations/SKILL.md`** — added TikTok/Twitter as Coming Soon; ad integrations now list BOTH benefits (keyword/creative-informed variants + ad-to-page personalization).
- [x] **`google-ads`, `meta-ads`** — explicit two-benefit framing ("Use keywords to generate better variants" / "Use creatives to generate better variants" + personalization).
- [x] **`dalton-setup` Phase 0** — cold-start paths verified with real URLs; added Page Settings gear as primary install-status check.
- [x] **`results-interpretation`** — added operator how-to for pausing/resuming experiments and adjusting holdout traffic (with the "changes affect all pages" warning).
- [x] **`dalton-help`** — added "Quick operator shortcuts" block with five most-common direct actions, plus `results-interpretation` routing row.

## Browser-exploration findings also worth noting

- **Brand Intelligence** page has more sections than docs describe: Brand Description, Industry, Keywords, Important Benefits, Remarks & Guidelines (where brand rules / restricted phrasing lives), Brand Assets (files and URLs), Competitors, Brand Style (palette + screenshot). A future update to `creating-experiments` could cover the full set; today it references Brand Intelligence generically.
- **User menu (top-right avatar)** surfaces Manage Users — candidate for a future `dalton-account` / team-management skill (currently in "Future work" below).
- **Demo-data import/export** appears in the user menu (Export Demo Data / Import Demo Data) — Dalton-internal tooling. Skip for customer-facing skills; may be useful for internal agents.

## Future work (not blocking, but noted for completeness)

Not currently covered in any skill. Scope with user if/when needed:

- **Billing / plan / pricing details** — currently deflected to Calendly demo link via `dalton-info`. A dedicated billing skill would cover plan limits, upgrades, invoices.
- **Team / user invites** — Manage Users exists in the user menu. Dedicated skill would cover roles/permissions.
- **API / webhooks / programmatic use** — if Dalton exposes these.
- **Account recovery / password reset** — typical auth self-service flows.
- **Experiment history / audit trail** — viewing previous variant versions, explicit undo UI.

## Future work (not blocking, but noted for completeness)

Not currently covered in any skill. Scope with user if/when needed:

- **Billing / plan / pricing details** — currently deflected to the Calendly demo link via `dalton-info`. A dedicated billing skill would cover plan limits, upgrades, invoices.
- **Team / user invites** — adding teammates, role management, permissions.
- **API / webhooks / programmatic use** — if Dalton exposes these.
- **Account recovery / password reset** — typical auth self-service flows.
- **Experiment history / audit trail** — viewing previous variant versions, explicit undo UI.

## Working agreement

- One skill family at a time; user picks order.
- Vibe-based iteration — no eval harness, no subagent runs. Draft → review → revise.
- Audience: primarily Dalton customers (marketing/CRO), secondarily internal team.
- **Operator-mode default**: if the consuming agent has browser/filesystem tools, skills instruct it to *do* the task on the user's behalf (via `platform.getdalton.com` and the editor URL pattern). Manual walkthrough is the fallback.
- Permission etiquette: state → confirm → execute → report. Never launch experiments or push live changes without explicit user confirmation.
- Support escalation: Intercom on `platform.getdalton.com` is the default. Dedicated Slack/Teams channel is a conditional fallback (not all customers have one).
- Descriptions "pushy" on when to trigger, explicit about when NOT to, so sibling skills don't overlap.
