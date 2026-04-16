---
name: dalton-google-analytics
description: Connect Dalton to Google Analytics 4 and set up the two things that unlocks — (a) using GA4 events as Dalton conversion goals, and (b) creating Optimized-vs-Control audiences in GA4 so the user can compare Dalton's impact in any GA4 report. Use this skill whenever GA4 is involved — including "connect GA4", "use a GA4 event / purchase event / signup event as my goal", "how do I create the dalton audiences", "compare optimized vs control in GA4", "measure Dalton's overall lift". Covers the holdout group (default 20% control / 80% optimized, adjustable). If the user is deciding *which goal type* to pick (not GA4-specific), route to `dalton-page-goals` instead; this skill owns the GA4-side work once GA4 is the chosen goal source.
---

# Dalton ↔ Google Analytics 4 integration

Use this skill to wire Dalton to GA4. Two separate outcomes — they're often set up together but solve different problems:

1. **Pull GA4 events as Dalton conversion goals.** Let Dalton optimize for purchases, signups, or any GA4 custom event — not just button clicks.
2. **Create Optimized-vs-Control GA4 audiences.** See Dalton's cross-product impact in any GA4 report (landing pages, revenue, retention, etc.) by comparing the two groups.

Most users want both. Do #1 first if they want to launch an experiment; do #2 first if they want reporting / exec-friendly analysis.

## The holdout group (what "Optimized vs Control" means)

Dalton splits traffic into two groups by default:

- **Optimized (80%)** — sees Dalton's experiments
- **Control (20%)** — sees the original pages, no experiments at all

The split is **adjustable in Dalton settings** — but the default is 20/80 and the skill should keep that as the working assumption unless the user has explicitly changed it.

Every session fires a `dalton` event to GA4 with a `type` dimension set to either `control` or `optimized`. Audiences in GA4 filter on that event + dimension.

## Outcome 1 — GA4 events as Dalton conversion goals

### Why

Optimizing for a downstream action (a purchase, a confirmed signup) is better than optimizing for a proxy (a button click). If GA4 already tracks the purchase/signup event, use it as the goal.

### Operator flow

1. **Confirm GA4 is connected in Dalton.** Navigate to `https://platform.getdalton.com` → **Integrations**. If Google Analytics isn't connected, click **Connect Google Analytics**, complete OAuth, and confirm it flips to Connected.
2. **Ensure the target event exists in GA4.** Have the user name the event (e.g. `purchase`, `generate_lead`, `sign_up`, or a custom event name). If uncertain, go to GA4 → **Admin → Events** or **Realtime** and verify the event fires in the last 24h.
3. **Set the goal in Dalton.** In the experiment's settings, pick the GA4 event as the conversion goal (→ hand off to `dalton-page-goals` for the goal-selection details).
4. **Verify.** Trigger the event once manually (buy a $0.01 test product, submit a demo form) and confirm Dalton registers the conversion within a few minutes.

### Common mistakes

- **Picking a pageview as a conversion when a GA4 event is available.** The event is usually more precise — prefer it.
- **Picking an event that isn't firing consistently.** Dalton can only optimize for what it receives. Verify in GA4 Realtime first.
- **Conflicting attribution windows.** Dalton and GA4 have different conversion attribution windows, so absolute numbers won't match exactly. This is normal — Dalton's **relative** comparison (variant vs baseline) remains accurate. Don't let users panic over the mismatch.

## Outcome 2 — Optimized vs Control GA4 audiences

### Why

The user wants to see Dalton's overall impact in GA4 — not at the per-variant level, but at the "is Dalton lifting our whole funnel" level. Two audiences (Optimized and Control) let them compare groups in any GA4 report.

### Operator flow

Can be driven via browser automation across **both** Dalton and the GA4 UI at `analytics.google.com`.

#### A. Confirm Dalton is emitting the event

1. Open the user's live site in the browser tool.
2. Check GA4's **Realtime** report (Reports → Realtime) — filter by event name `dalton`. It should fire on page load.
3. If it doesn't, check install (`dalton-script-install`) and confirm the user has accepted cookies (session tracking requires consent).

#### B. Create the "Optimized" audience in GA4

Navigate: GA4 → **Admin → Audiences → New Audience → Create a custom audience**.

1. **Source:** Events
2. **Event name:** `dalton`
3. **Condition:** `type = optimized`
4. Name the audience something like **"Dalton Optimized"** so it's findable later.
5. Save.

#### C. Create the "Control" audience

Repeat B with `type = control`. Name it **"Dalton Control"**.

#### D. Verify audiences are populating

Audiences take up to 24–48 hours to populate. Set expectations with the user — they won't see numbers immediately. In the meantime, confirm they were created correctly (the event + dimension filter shown in the audience definition).

#### E. Use the audiences in reports

In any GA4 report (Landing Pages, Pages & Screens, Monetization, Retention):

1. Click **Add Comparison** at the top.
2. Create comparison groups based on **Audience Name** — one for Optimized, one for Control.
3. The report now shows side-by-side metrics for the two groups.

This is the dashboard users point at when asked "what's Dalton doing for us?"

### Manual walkthrough (fallback)

If you can't drive GA4:

1. Tell the user to go to **GA4 → Admin → Audiences → New Audience → Create a custom audience**.
2. Walk them through setting source = Events, event name = `dalton`, condition = `type = optimized`. Save as "Dalton Optimized".
3. Repeat with `type = control` for "Dalton Control".
4. Tell them it takes 24–48 hours for audiences to populate.
5. Show them how to Add Comparison in any report and filter by Audience Name.

## Common GA4 integration problems

- **No `dalton` events in GA4.** Either the Dalton snippet isn't installed (→ `dalton-script-install`), or GA4 isn't capturing it (check GA4 stream config, content-security policies, adblockers during testing).
- **Audience populating slowly.** Normal. GA4 audiences can take 24–48 hours. Not a bug.
- **Numbers don't match Dalton dashboard.** Expected. Different attribution windows and bot handling. The relative comparison still works.
- **User expected Dalton to auto-create the audiences.** It doesn't — the user has to create them in GA4. (Some users assume Dalton does it for them because the Dalton side "just works.")

## Permission etiquette

- When connecting GA4 from Dalton, let the user complete OAuth themselves.
- When creating audiences in GA4, describe what you're about to do and get confirmation — audiences don't affect data retroactively and aren't destructive, but they're user-visible.
- Don't modify GA4 conversion settings, data streams, or property-level configuration without explicit permission. Stay in Audiences and Reports.

## What *not* to do

- **Don't invent GA4 measurement IDs or event names.** If the event the user names doesn't exist, tell them and work with their real events.
- **Don't promise absolute-number parity between Dalton and GA4.** Different tools, different attribution. Set that expectation.
- **Don't create audiences with subtly wrong filters.** Re-reading the audience definition is worth the 10 seconds.
- **Don't launch experiments using GA4-event goals before verifying the event actually fires.**

## When to hand off to another skill

- User hasn't installed Dalton on the page whose events they want to track → `dalton-script-install`
- User is specifically picking a goal type (not the GA4 connection itself) → `dalton-page-goals`
- User wants ad-driven personalization, not analytics → `dalton-google-ads` or `dalton-meta-ads`
- Integration is connected but data isn't flowing → `dalton-help`

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `google-analytics-tracking.md` — the canonical GA4 audience setup
- `technical/analytics-integration.md` — GA4 integration overview including holdout group
- `optimization/conversion-goals.md` — GA4 events as goals
- `faq/integrations.md` — GA4-related FAQ
