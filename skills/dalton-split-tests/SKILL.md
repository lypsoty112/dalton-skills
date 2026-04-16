---
name: dalton-split-tests
description: Configure a Dalton URL Split Test — comparing entirely different page URLs head-to-head (not element-level variants on one page). Use this skill whenever someone asks about URL split tests, testing a full redesign, comparing two or more different landing pages, A/B testing different templates, testing pages Dalton can't modify via the editor (images, videos, hero redesigns, heavy layout changes), or choosing between a normal experiment and a split test. With browser automation, drive the 4-step wizard at platform.getdalton.com/admin/[customer_id]/traffic-split/new; without it, coach the user through the same flow.
---

# Dalton URL Split Tests

Use this skill when the user wants to compare **entirely different pages**, not element-level changes on one page. URL Split Tests route traffic across multiple URLs and measure which converts best against a shared goal. Element-level experiments are a different skill (`dalton-creating-experiments`).

## When to use a URL Split Test vs. a regular experiment

| Situation | Use |
|---|---|
| Change headline / CTA copy / move a section | **Regular experiment** (`dalton-creating-experiments`) |
| Test a hero image or video | **URL Split Test** (editor can't edit images) |
| Compare two completely different page designs | **URL Split Test** |
| Test a heavy redesign against the current page | **URL Split Test** |
| Two legitimately different templates for the same conversion goal | **URL Split Test** |

**Rule:** if what the user wants to test is expressible as AI-generated variants of one page, use a regular experiment. If it only makes sense as fully-built alternate pages, use a split test.

## Where split tests live on the platform

From the sidebar, **Split Tests** opens:

```
https://platform.getdalton.com/admin/[customer_id]/traffic-split
```

Create a new split test at:

```
https://platform.getdalton.com/admin/[customer_id]/traffic-split/new
```

The create flow is a 4-step wizard: **Name → URLs → Conversion Goal → Traffic Split**.

## How a URL Split Test actually works

1. User builds the alternate pages themselves — Dalton does not generate them.
2. User lists the URLs in the wizard (2, 3, or more).
3. User picks a traffic distribution mode.
4. User sets a conversion goal (shared across all URLs).
5. Dalton routes incoming traffic across the URLs and measures conversions.

No additional install — if the Dalton snippet is in `<head>` on each URL, split tests work automatically.

## The three traffic distribution modes

| Mode | Behavior | Best for |
|---|---|---|
| **Fixed Split** | Equal traffic across all URLs for the duration | Rigorous analysis, equal sample sizes |
| **Smart Optimization** | Gradually shifts traffic toward winners while still learning | **Default recommendation** — balances learning with results |
| **Aggressive Optimization** | Rapidly pushes traffic to the top performer | Time-sensitive campaigns (Black Friday, limited promos) |

Default to **Smart Optimization** unless the user has a specific reason otherwise.

## Default path: drive the 4-step wizard

Operator flow assuming browser automation and the user logged in.

### Step 0 — sanity check

Make sure this really should be a URL Split Test. If they're just changing a headline, route to `dalton-creating-experiments`.

### Step 1 — verify each URL

For each URL the user wants to include:
1. Open it in the browser tool.
2. Confirm it loads (no 404, no staging password wall).
3. Confirm the Dalton snippet fires (network request to `cdn.getdalton.com`, or `window.dalton` defined). If not, route to `dalton-script-install` before proceeding.
4. Confirm the conversion target (button / path / event) exists on the URL.

### Step 2 — open the wizard

Navigate to `https://platform.getdalton.com/admin/[customer_id]/traffic-split/new`.

### Step 3 — drive the wizard

**Name** — paste a descriptive name (e.g. "Homepage v2 vs. current — Q2 test"). Click Next.

**URLs** — add each URL. Minimum 2, no maximum. Next.

**Conversion Goal** — the goal is shared across all URLs in the test. The same four goal types apply as for regular experiments (plus GA4 events when GA4 is connected) — hand off to `dalton-page-goals` if goal selection is unclear. Next.

**Traffic Split** — pick Fixed / Smart / Aggressive. Default to Smart unless specified otherwise. Next / Launch.

### Step 4 — pre-launch QA

Don't click Launch before verifying:

- Each URL loads correctly on desktop and mobile.
- Dalton is installed on all of them.
- The conversion goal would fire consistently across all URLs (e.g. if goal is page-view on `/thank-you`, all URLs funnel there).
- The mode choice matches user intent.

Get explicit user confirmation, then launch.

## Manual walkthrough (fallback)

If you can't drive the browser:

1. Dashboard sidebar → **Split Tests** → **Create URL Split Test**.
2. Fill out the 4 steps: Name → URLs → Conversion Goal → Traffic Split mode.
3. Verify each URL is reachable, Dalton-installed, and renders correctly on desktop/mobile.
4. Launch.

## Setup caveats

- **All URLs need Dalton installed.** The snippet on the entry page sets the cookie, but every URL in the test should have it to capture session data. Missing on any URL → route to `dalton-script-install`.
- **The conversion goal must fire consistently across URLs.** If the goal is page-view on `/thank-you`, the flow from each URL has to reach that page, and Dalton must be on it.
- **Give it time.** Even Smart / Aggressive mode should run for ≥1–2 weeks. Don't let impatient users pull results after 2 days.
- **Test meaningful differences.** Two URLs that differ by a single word shouldn't be a split test — use an element-level experiment.

## Common mistakes

- **Split-testing minor tweaks.** If the difference is a headline or CTA, a regular experiment is faster and better.
- **Missing snippet on one URL.** Silent failure — Dalton can't measure conversions there. Always verify on every URL before launch.
- **Different goals per URL.** The goal is shared. If each URL has a structurally different conversion path, you can't meaningfully compare them on one goal.
- **Mode mismatch.** Picking Aggressive for a test where rigorous data was the goal, or Fixed for a time-critical campaign.

## Permission etiquette

Split tests re-route live traffic to alternate URLs that the user built. Before launch:

- Confirm the user genuinely intends to send traffic to each listed URL (staging URLs are a classic mistake).
- Confirm the mode choice matches intent.
- State what you're about to do, get confirmation, report after.

## What *not* to do

- **Don't recommend a split test** when a regular experiment would do.
- **Don't list a URL you haven't verified** loads and has Dalton installed.
- **Don't default to Aggressive mode.**
- **Don't launch** without triggering the goal on each URL at least once to confirm it fires.

## When to hand off to another skill

- Changes are element-level, not whole-page → `dalton-creating-experiments`
- Snippet missing on one URL → `dalton-script-install`
- Setting / changing the conversion goal → `dalton-page-goals`
- Test is running but numbers look weird → `dalton-results-interpretation`
- Multi-page experiments (same element change across many URLs via wildcard) is NOT a split test → use `dalton-copying-experiments`

## Canonical sources

- Platform URL map: `skills/references/platform-urls.md`
- Docs: `advanced/url-split-tests.md`, `split-tests.md`, `advanced/multi-page-experiments.md` under `~/Documents/dalton/dalton-docs/`
