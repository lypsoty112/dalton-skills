---
name: dalton-integrations
description: Connect Dalton to external platforms — Google Analytics (GA4), Google Ads, Meta Ads. Use this skill as the entry point whenever someone wants to wire Dalton up to analytics or ad platforms, pull GA4 events as conversion goals, compare Optimized vs Control audiences in GA4 reports, or personalize landing pages based on which ad a visitor clicked. Routes to google-analytics, google-ads, or meta-ads sub-skills. Also covers the big picture: what each integration unlocks and which one to reach for.
---

# Dalton integrations (family root)

Router for the integrations family. Each integration solves a different problem — don't route to the wrong one.

## What each integration does

| Integration | What it unlocks | Status |
|---|---|---|
| **Google Analytics (GA4)** | Use GA4 events as Dalton conversion goals. Compare Dalton's **Optimized** audience (80% of traffic by default) against **Control** (20%) in any GA4 report. | GA |
| **Google Ads** | (1) **Use keywords to generate better variants** — Dalton reads your campaign keywords and adapts variants to them. (2) **Ad-to-page personalization** — match landing pages to specific Google Ads campaigns / ad groups / creatives. | Beta |
| **Meta Ads (Facebook/Instagram)** | (1) **Use creatives to generate better variants** — Dalton reads your ad creatives and adapts variants to them. (2) **Ad-to-page personalization** for Meta ad campaigns. | Beta |
| **TikTok Ads** | Ad-to-page personalization planned. | Coming Soon |
| **Twitter Ads** | Ad-to-page personalization planned. | Coming Soon |

GA4 is the most common integration and the one most users should set up first — it's free, takes a few minutes, and unlocks cross-report comparisons. Google Ads / Meta Ads are for users running paid acquisition who want landing pages that match each creative AND who want their keywords/creatives to inform variant generation. If the user asks about TikTok or Twitter Ads, be honest: "coming soon" — no timeline promised.

## Which sub-skill to use

| User is asking about | Use |
|---|---|
| Connecting GA4, using GA4 events as goals, creating GA4 audiences for Optimized vs Control, comparing groups in GA4 reports | `google-analytics` |
| Connecting Google Ads, setting up tracking template at account level, campaign/ad set/creative-level personalization for Google Ads traffic | `google-ads` |
| Connecting Meta Ads, setting up per-ad URL parameters (Meta doesn't support account-level tracking templates), personalization for Meta ad traffic | `meta-ads` |

## How ad-to-page personalization works (shared concept)

Google Ads and Meta Ads both feed into the same personalization system. The flow is identical across both platforms:

1. **Connect the ad account** in Dalton (Integrations tab → Authorize).
2. **Select landing pages** that receive ad traffic.
3. **Choose a personalization level:**
   - **Campaign** — match page to campaign theme (broad messaging, seasonal)
   - **Ad Set** — customize for audience segments (demographics, locations, interests)
   - **Creative** — match individual ad creatives (max message-match, smallest per-audience)
4. **AI groups similar ads** and extracts key messaging.
5. **Dalton generates variants** with headlines, CTAs, and content that mirror the ad copy.
6. **User approves** before anything goes live.

**Where the two platforms differ is tracking setup:**

- **Google Ads** supports an **account-level tracking template** — set it once, all ads inherit the required URL parameters.
- **Meta Ads** requires **per-ad URL parameters** — Meta doesn't support account-level tracking templates. Tedious but one-time-per-ad.

The `google-ads` and `meta-ads` sub-skills cover the exact parameter format for each.

## Canonical parameter structure (both platforms)

Dalton recognizes these URL parameters (names are suggestions — any names work; Dalton detects the values):

- `campaign_id` — ID of the campaign
- `adset_id` — ID of the ad set (or ad group for Google)
- `creative_id` — ID of the specific creative (or ad)

Always send **all three** so the user can switch personalization levels later without reworking tracking. The sub-skills show the exact templates.

## Permission etiquette

Integrations touch third-party accounts (Google, Meta). Before connecting:

- Tell the user what OAuth scopes Dalton will request.
- Let them walk through the auth handoff themselves — don't try to bypass OAuth in a browser tool.
- After connecting, verify the integration shows "Connected" in Dalton and that data starts flowing.

## When to hand off to another skill

- Dalton snippet isn't installed on the target pages → `dalton-script-install`
- User wants GA4 event as a conversion goal → the `google-analytics` sub-skill will cover both connecting AND using events as goals; if goal-setup is the specific question → `dalton-page-goals`
- User hasn't created the experiments they want to personalize → `creating-experiments`
- Integration seems connected but data isn't flowing → `dalton-help`

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `google-analytics-tracking.md` — GA4 audience setup walkthrough
- `technical/analytics-integration.md` — GA4 integration overview including holdout explanation
- `_drafts/ad-personalization.md` — ad personalization spec (Google + Meta)
- `_drafts/personalization-setup.md` — tracking template (Google) and per-ad URL (Meta) setup
- `faq/integrations.md` — integrations FAQ
