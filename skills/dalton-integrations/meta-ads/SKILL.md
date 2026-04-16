---
name: meta-ads
description: Connect Meta Ads (Facebook/Instagram) to Dalton for ad-to-page personalization — landing pages that match the specific Meta campaign / ad set / ad a visitor clicked. Covers per-ad URL parameter setup (Meta does NOT support account-level tracking templates like Google does), connecting the Meta Ads account in Dalton, picking a personalization level, and generating personalized variants. Use this skill whenever someone asks about Meta / Facebook / Instagram Ads integration, ad personalization for Meta campaigns, tracking URL setup for Meta, or wiring Meta Ads into Dalton. Feature is Beta.
---

# Dalton ↔ Meta Ads integration

Use this skill to wire Meta (Facebook + Instagram) Ads into Dalton so landing pages personalize to the ad each visitor clicked. Beta feature — flag that up front if the user asks about reliability or SLA.

**Key difference from Google Ads:** Meta has no account-level tracking template. URL parameters must be set **per ad** in Ads Manager. This is more tedious, so lean on the per-ad workflow hard — don't recommend bulk approaches that don't exist in Meta.

## What this integration does

Two benefits, as the Integrations panel spells out:

1. **"Use creatives to generate better variants"** — Dalton reads your ad creatives and uses them as context when generating variants. Variants get smarter without extra prompt work.
2. **"Allow for ad-to-page personalization"** — landing pages dynamically match the campaign / ad set / ad a visitor clicked.

Downstream benefits:

- Better conversion rates from consistent ad → page message-match
- More efficient ad spend — Meta's learning algorithms reward consistent user experience
- No need to build a separate landing page per creative

Tell the user about both. (1) is free once connected; (2) requires the per-ad URL parameters below.

## How the flow works

1. User **adds URL parameters to each ad** in Meta Ads Manager so clicks land on URLs carrying `campaign_id`, `adset_id`, `creative_id`.
2. User **connects Meta Ads in Dalton** via OAuth.
3. Dalton **scans connected campaigns** and shows which landing pages receive ad traffic.
4. User **picks a personalization level** (campaign / ad set / ad — Meta calls the unit of creative "ad").
5. Dalton's **AI groups similar ads** and extracts messaging.
6. Dalton **generates personalized variants**.
7. User **reviews and approves** before launch.

## The per-ad URL setup (critical prerequisite)

Meta **does not** support account-level tracking templates. URL parameters are set per ad, at the **Ad level** (not Campaign or Ad Set level) in Ads Manager.

### The recommended URL format

```
https://yoursite.com/landing-page?campaign_id={{campaign.id}}&adset_id={{adset.id}}&creative_id={{ad.id}}
```

- `{{campaign.id}}` — Meta's placeholder for the campaign ID
- `{{adset.id}}` — ad set ID
- `{{ad.id}}` — individual ad ID

Important:

- **Always include all three parameters**, even if the user starts at campaign-level personalization. Switching levels later doesn't require re-editing every ad.
- **Parameter names are arbitrary** (Dalton detects values regardless), but use these defaults unless there's a reason otherwise.
- **Parameters go in the Website URL field**, at the Ad level.
- **Every ad needs them.** New ads created after setup also need them — there's no inheritance.

### Where in Ads Manager

1. Open Ads Manager.
2. Navigate to the **Ad** level (not Campaign, not Ad Set).
3. In **Destination → Website URL**, append the parameters.
4. Publish.

If an ad uses an "Existing URL" template or a saved URL preset, the parameters need to be added to that preset too — otherwise they'll drop off.

## Default path: operator flow

Assuming browser automation.

### Step 1 — decide scope

Ask: is this for all ads in a specific campaign, or all ads across the account?

- **Existing campaigns with many ads** — this is going to be tedious. Discuss bulk-edit options in Ads Manager (you can edit multiple ads at once in the table view). Still per-ad — just batch-applied.
- **New campaigns** — set up ad templates / URL presets with the parameters baked in, so new ads inherit them.
- **One-campaign pilot** — start narrow. Pick one campaign, one landing page, wire it up end-to-end, then expand.

### Step 2 — add URL parameters to each ad

1. In Ads Manager, go to **Ads** view (not Campaigns or Ad Sets).
2. Filter to the ads needing the update.
3. For each ad (or select multiple for bulk edit):
   - Edit → **Destination → Website URL** → append the parameter block
   - Publish
4. If the account has **URL presets**, update the preset to include the parameters so new ads inherit them automatically.

### Step 3 — verify parameters reach the landing page

1. Copy one ad's final URL from Ads Manager (the preview shows the resolved URL with placeholders filled in).
2. Paste it into the browser tool and verify the landing page URL contains all three parameters with real values (non-empty).

If parameters are missing or empty, fix before proceeding. Nothing downstream works without this.

### Step 4 — connect Meta Ads in Dalton

1. Navigate to `https://platform.getdalton.com` → **Integrations**.
2. Click **Connect Meta Ads** (or "Connect Facebook Ads" — same thing). User completes OAuth.
3. After OAuth, confirm Dalton shows Connected and displays the landing pages receiving Meta traffic.

### Step 5 — pick pages and personalization level

1. Select landing page(s) to personalize.
2. Choose a level:

| Level | What it does | Best for |
|---|---|---|
| **Campaign** | One variant per campaign | Seasonal / broad messaging, smallest variant count |
| **Ad Set** | One variant per audience segment | Demographic / interest / location segmentation |
| **Ad** | One variant per creative | Testing angles, maximum message-match, most variants |

Default recommendation: **campaign-level** for first-time setup. Scale to ad-set or ad-level as the user gets comfortable.

### Step 6 — AI generates variants

Dalton groups similar ads, extracts messaging, generates variants per group. Headlines, CTAs, and content tailored to each segment.

### Step 7 — review, approve, launch

- Preview each variant on desktop and mobile.
- Read AI output for accuracy, brand fit, compliance.
- Approve per-variant. Launch on explicit user confirmation.

## Dalton flags ads missing parameters

Once connected, Dalton shows which ads have correct tracking and which don't. Use this list to close the loop — any flagged ad won't participate in personalization. Users often miss one or two when setting up manually.

## Common mistakes

- **Setting parameters at the Campaign or Ad Set level.** Meta only applies URL parameters set at the **Ad** level. Campaign/ad-set-level URL fields exist but don't behave the way users expect.
- **Assuming account-level tracking like Google Ads.** Doesn't exist on Meta. Per-ad only.
- **Forgetting to update new ads.** Parameters aren't automatically applied to ads created after setup. Either update a URL preset, or build it into the team's ad-creation checklist.
- **Hard-coding IDs.** Never write a literal `campaign_id=12345`. Always use Meta's placeholders (`{{campaign.id}}` etc.) — they auto-fill per ad.
- **Starting at ad-level personalization with hundreds of ads.** Too many variants. Start campaign-level.
- **Launching personalization before verifying parameters reach the page.**

## Permission etiquette

This integration touches a live ad spend account.

- Updating Website URLs in ads is a live change — state what you're about to do, get confirmation.
- Bulk-editing many ads at once — pause, show the user a small sample first, confirm before scaling.
- Meta OAuth: let the user complete it themselves.
- Never approve variants for launch without explicit user confirmation.

## What *not* to do

- **Don't edit ad copy, creative, or budget.** Stay in the URL field.
- **Don't claim Meta has account-level tracking templates.** It doesn't.
- **Don't skip the parameter verification step.**
- **Don't promise specific ROAS numbers.** Cite the mechanism, not guaranteed outcomes.
- **Don't recommend third-party URL-parameter tools for Meta** when in-platform bulk edit does the job.

## When to hand off to another skill

- Landing page isn't installed with Dalton → `dalton-script-install`
- User wants cross-campaign reporting in GA4 → `google-analytics`
- User wants Google Ads personalization too → `google-ads` (different tracking setup — account-level template)
- Generated variants need tweaking → `editing-experiments`
- Integration connected but variants aren't serving → `dalton-help`

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `_drafts/ad-personalization.md` — ad personalization walkthrough (draft status, but canonical for this feature)
- `_drafts/personalization-setup.md` — per-ad URL setup specifics for Meta
- `_drafts/personalization.md` — personalization deep-dive
- `faq/integrations.md` — integrations FAQ