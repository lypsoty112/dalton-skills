---
name: dalton-help
description: Help a user troubleshoot a Dalton problem or reach Dalton support. Use this skill when something isn't *working* — "script not detected", "variants not showing", "no data in dashboard", "prompt errors", "preview looks weird", "mobile looks wrong", "how do I contact Dalton". If data IS flowing but the user just doesn't understand the numbers (what does lift mean, is this significant, should I act), route to `dalton-results-interpretation` instead. With browser automation, reproduce the symptom and apply the fix yourself; without it, coach the user. Triage first (what they see, where, what changed, what they've tried) before guessing at a fix.
---

# Dalton help & troubleshooting

Use this skill when a user is stuck, confused, or wants to reach a human at Dalton. Two goals: **resolve the common stuff fast**, and **route to the right support channel** when you can't.

## How to approach a help request

Before guessing, triage. Get (or extract from the conversation) the four facts that make or break every support ticket:

1. **What they're seeing** — exact error text, screenshot, or behavior.
2. **Where** — page URL + browser + desktop/mobile.
3. **What changed recently** — new install, new variant, new deploy on their side.
4. **What they've already tried** — incognito? cache clear? different browser?

Lead with the single most likely fix. Don't stack five possible fixes in one message — it's overwhelming and makes it hard to tell which one worked.

## Default path: verify and fix for them

Assume you (the agent) have browser automation (`mcp__claude-in-chrome__*`, Playwright, etc.) and can log into `platform.getdalton.com` on the user's behalf. Reproducing the symptom yourself beats narrating DevTools steps.

Operator playbook:

1. **Reproduce the symptom.** Open the affected page in the browser tool. Use incognito-style context when possible to bypass their browser cache. Watch for:
   - Network requests containing `dalton` (is the script firing?)
   - Console errors
   - Whether variants actually appear on the page vs. the baseline
2. **Check the Dalton side.** Navigate to `platform.getdalton.com`, open the relevant experiment, and confirm: Live status, correct URL targeting, a configured conversion goal, data in the dashboard, and whether the experiment is past its learning phase (~250 sessions and ~100 conversions).
3. **Identify which row in the triage table below matches the symptom.** Apply that fix.
4. **Re-verify.** Re-load the page and confirm the symptom is gone. Report clearly what you changed and what the user should do to confirm on their side.

If you have no browser tools, fall back to coaching the user — the triage table still applies; they just do the clicking.

## Quick triage table

| Symptom | Most likely cause | First thing to try |
|---|---|---|
| "Installation required" banner won't go away | Script not in `<head>`, or page cached | Confirm script is in `<head>` (not `<body>`), clear cache, hard-reload. If it still fails → `dalton-script-install`. |
| Script installed but variants don't show | Experiment paused, or page cached | Open in incognito. If variants appear there, it's caching on the user's side. Also check experiment is Live and targets the right URL. |
| "Can't find location to place experiment" in editor | Element inside iframe, shadow DOM, or loads via JS after page load | Select a parent element instead, or test if the element exists in View Source. |
| Prompt returns an error or weird result | Prompt is vague, or element is dynamic (price, inventory, size selector) | Re-prompt more specifically (e.g. "Make this one sentence, under 10 words"), avoid dynamic elements. |
| Mobile variant looks broken but desktop is fine | AI optimized for whichever viewport was active when the prompt ran | Preview both viewports before launch. Use "on mobile only" in prompts for mobile-specific changes. |
| Dashboard shows zero sessions/conversions | Script not firing, experiment not Live, conversion goal not configured, or <24h since launch | Check in order: script loads → experiment Live → goal configured → wait 24–48h. |
| Certainty stuck / "significance taking too long" | Too little traffic, too many variants, or low conversion rate | Reduce variants, focus on higher-traffic pages, or optimize for click-through instead of purchase. |
| Numbers don't match Google Analytics | Different attribution windows, sampling, bot handling | Normal. Dalton's **relative** comparison (variant vs. baseline) remains accurate even if absolute numbers differ. |
| Preview looks slow or different from live | Preview mode has debugging overhead | Open the preview link in incognito. Live visitors see <50ms changes. |
| Keep seeing the same variant in preview | Variant assignment is sticky via cookie | Use incognito or clear cookies to see a different variant. |

## The usual suspects (things users forget)

Roughly 80% of tickets are one of these — walk through them before escalating:

- **Cache.** Hard-reload or incognito first. Always.
- **The `<head>` check.** Script must be in `<head>`, ideally high up.
- **Cookie consent.** Variants show regardless of consent, but **session tracking** requires cookies. Visitors who reject cookies don't appear in the dashboard.
- **Iframes and shadow DOM.** Dalton can't target inside them — use a parent in the main document.
- **Dynamic content.** Prices, stock levels, size selectors, personalized strings — Dalton won't reliably target these.
- **Live vs paused.** A freshly created experiment is not live by default.
- **24–48h delay.** Dashboard data isn't instant.
- **Learning phase.** Even with data flowing, Dalton waits for ~250 sessions and ~100 conversions before its estimates stabilize. Early numbers swing wildly — that's normal, not a bug.

## Prompt not working? Quick rewrite recipe

Most "prompt not working" reports resolve with a more specific prompt. Transform vague → specific:

- "Make it better" → "Rewrite the headline as a question under 10 words emphasizing free shipping"
- "Shorter" → "Cut to one sentence, keep the word *guaranteed*"
- "More urgent" → "Add 'ends Friday' to the CTA button"
- "Fix mobile" → "On mobile only, make the hero headline two lines max"

Also: **one change per prompt**. Headline + CTA + new badge = three variants, not one compound prompt.

## Where users can get more help

When a skill or agent can't resolve the issue, route to a human.

**Default for everyone:** the **Intercom widget in the bottom-right of `platform.getdalton.com`**. Every logged-in Dalton user has access. This is the right route 95% of the time.

**Conditional fallback:** some — not all — Dalton customers have a **dedicated shared Slack or Microsoft Teams channel** with the Dalton team. Don't assume this exists. Only mention it if the user brings it up, or frame it conditionally: *"if your account has a dedicated Dalton Slack/Teams channel, that's another fast route."*

**For sales / scoping / new-customer conversations** (not day-to-day bugs): the Calendly demo link — https://calendly.com/vincent-getdalton/30min.

When escalating, coach the user to include:

- Screenshot of the error
- Page URL
- Browser + OS + desktop/mobile
- Steps to reproduce
- What they've already tried

Example hand-off an agent can use:

> "I couldn't reproduce this from what you've described. Fastest path from here: open the Intercom chat in the bottom-right of `platform.getdalton.com`. Send them a screenshot, the page URL, your browser, and what we'd already tried. That way they can dig in without the back-and-forth."

Typical response time: within 24 hours on business days.

## What *not* to do

- **Don't execute changes to their live site or analytics wiring without explicit confirmation.** Recommend → confirm → execute → report.
- **Don't invent support URLs, phone numbers, or email addresses.** The channels above are the only ones.
- **Don't assume the user has a dedicated Slack/Teams channel with Dalton.** Most don't.
- **Don't tell users to "just reinstall"** unless you've confirmed the script truly isn't loading (via the browser tool or DevTools).
- **Don't overwhelm.** One most-likely fix per message, wait for their result, then stack more if needed.

## Quick operator shortcuts

Some fixes the agent runs directly from Dalton's UI (all assume browser automation and the user logged in):

- **Confirm install for a page** — open the page view, click the **settings gear** next to "Visual Editor". The Page Settings panel shows a green **Script Installed** badge (or not).
- **Pause / resume an experiment** — page view → `tab=data` → toggle the status pill. Changes revert instantly.
- **Check goal configuration** — page view → settings gear → **Optimization Goal** (pencil edit). Or inline via the page header's "Goal: [Type] [Edit]" link.
- **Reopen install snippet** — Integrations page → "Install Dalton Script for AB Testing & Tracking" → **View Instructions**.

Full URL / UI map in `skills/references/platform-urls.md`.

## When to hand off to another skill

- First-time **install** or install is broken → `dalton-script-install`
- **What Dalton is** / how it compares to A/B testing → `dalton-info`
- **How to create an experiment**, not "it's broken" → `dalton-editor` skill family
- **GA4 / Google Ads / Meta Ads** wiring specifically → `dalton-integrations` skill family
- **URL split tests** specifically → `dalton-split-tests`
- **Defining or changing the conversion goal** → `dalton-page-goals`
- **Interpreting live numbers** (data is flowing, user confused) → `dalton-results-interpretation`

## Canonical sources

Source of truth for help content (paths under `~/Documents/dalton/dalton-docs/`):

- `faq/troubleshooting.md` — full symptom/check/solution walkthrough
- `faq/setup.md` — install and platform-fit FAQ
- `faq/editor.md` — editor and prompt FAQ
- `faq/results.md` — results, traffic, significance FAQ
- `faq/integrations.md` — analytics and tooling FAQ

Read the relevant file when a user asks a specific factual question rather than guessing.
