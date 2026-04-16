---
name: dalton-google-ads
description: Connect Google Ads to Dalton for ad-to-page personalization — landing pages that match the specific Google Ads campaign / ad group / creative a visitor clicked. Covers setting up the account-level tracking template so all ads carry the required URL parameters (campaign_id / adset_id / creative_id), connecting the Google Ads account in Dalton, choosing a personalization level, and generating personalized page variants. Use this skill whenever someone asks about Google Ads integration, ad personalization, matching landing pages to ad creatives, Quality Score improvements via message-match, tracking template setup, or wiring Google Ads into Dalton. Feature is Beta.
---

# Dalton ↔ Google Ads integration

Use this skill to wire Google Ads into Dalton so landing pages personalize to the ad each visitor clicked. Beta feature — flag that to the user up front if they ask about reliability or SLA.

## What this integration does

Two benefits, as the Integrations panel spells out:

1. **"Use keywords to generate better variants"** — Dalton reads your campaign keywords and uses them as context when generating variants. Your variants get smarter without any extra prompt work.
2. **"Allow for ad-to-page personalization"** — landing pages dynamically match the campaign / ad group / creative a visitor clicked.

Downstream benefits:

- Better conversion rates from consistent message-match
- Improved Google Ads Quality Score → lower CPC over time
- No need to build a separate landing page per creative

Tell the user about both upfront. Some users only want (2), but (1) is free once the account is connected and often improves variant quality across the board.

## How the flow works

1. User **adds a tracking template** in their Google Ads account so ad clicks land on URLs carrying `campaign_id`, `adset_id`, `creative_id` parameters. (Account-level setup — once, applies to all ads.)
2. User **connects Google Ads in Dalton** via OAuth.
3. Dalton **scans connected campaigns** and shows which landing pages are receiving ad traffic.
4. User **picks a personalization level**: campaign / ad group / creative.
5. Dalton's **AI groups similar ads** and extracts their messaging.
6. Dalton **generates personalized variants** of the landing page per group.
7. User **reviews and approves** before launch.

## The tracking template (critical prerequisite)

This is the step users most often get wrong. Without the right URL parameters on incoming ad clicks, Dalton can't tell which ad brought the visitor in — so personalization can't work.

### Account-level tracking template

**Recommended template** (set once, inherits to every ad):

```
{lpurl}?campaign_id={campaignid}&adset_id={adgroupid}&creative_id={creative}
```

- `{lpurl}` — Google's placeholder for the ad's final URL (the user's actual landing page URL)
- `{campaignid}` — Google auto-fills the campaign ID
- `{adgroupid}` — auto-fills the ad group ID (Google uses "ad group"; Dalton calls this `adset_id` by convention)
- `{creative}` — auto-fills the creative (ad) ID

Important:

- **Always include all three parameters**, even if the user starts at campaign-level personalization. This lets them switch to ad-group or creative-level later without redoing tracking.
- **The parameter *names* (`campaign_id`, `adset_id`, `creative_id`) are arbitrary.** Dalton detects the values regardless of the parameter name. But these are the documented defaults — use them unless the user has existing tracking conventions.
- **Set it at the account level.** Per-ad setting is possible but tedious and error-prone. Don't recommend it unless there's a specific reason.

### Where in Google Ads

Navigate: **Google Ads → Account Settings → Tracking**. Paste the template. Save.

## Default path: operator flow

Assuming browser automation and access to both the Dalton dashboard and the Google Ads account (the user logs in to each via the browser tool).

### Step 1 — set the tracking template

1. Ask the user if the account already has a tracking template. If it does, read it and confirm it includes all three parameters. If not, help them add the template above.
2. Navigate to the Google Ads account → **Admin → Account Settings → Tracking**.
3. Add or update the tracking template to the recommended value.
4. Save. Ask the user to confirm the change — this is an account-wide setting.

### Step 2 — verify parameters reach the landing page

1. Pick one active ad in the account.
2. Use the **Ad Preview and Diagnosis** tool (or copy the final URL directly) and simulate a click.
3. Check the resulting URL contains all three parameters with non-empty values.

If the parameters aren't showing up, fix the tracking template before proceeding — nothing downstream will work without it.

### Step 3 — connect Google Ads in Dalton

1. Navigate to `https://platform.getdalton.com` → **Integrations**.
2. Click **Connect Google Ads**. User completes OAuth.
3. After returning, confirm the integration shows Connected and Dalton displays a list of campaigns / landing pages receiving traffic.

### Step 4 — pick pages and personalization level

1. Select the landing page(s) the user wants to personalize.
2. Choose a personalization level:

| Level | What it does | Best for |
|---|---|---|
| **Campaign** | One variant per campaign theme | Seasonal campaigns, broad messaging alignment |
| **Ad Group** (Dalton calls this "Ad Set") | One variant per audience segment | Demographics, locations, interests, device targeting |
| **Creative** | One variant per individual ad creative | Testing angles, product-specific variants, maximum message-match |

Start with **campaign** for most users — fewer variants, easier to review, still gets most of the lift. Move to creative-level once they're comfortable.

### Step 5 — AI generates variants

Dalton groups similar ads, extracts messaging, and produces variant drafts per group. The variants include:

- Headlines mirroring ad copy
- CTAs aligned with the ad's promise
- Content sections tailored to each segment

### Step 6 — review, approve, launch

- Preview each variant on desktop and mobile.
- Read AI output for accuracy and brand fit.
- Approve per-variant. Launch on explicit user confirmation.

Dalton's **Results Dashboard** + the `dalton-google-analytics` audiences handle measurement.

## Dalton verifies tracking automatically

Once Google Ads is connected, Dalton flags any ads missing the required parameters. Treat this as a canary — if users see flagged ads, the tracking template didn't apply to them (common cause: ad was created before the template was set, or has a per-ad URL override).

## Common mistakes

- **Setting only some of the three parameters.** Breaks personalization if the user later switches levels. Always set all three from the start.
- **Using the template at per-ad level instead of account level.** Tedious, easy to miss ads. Set at account level.
- **Editing the landing-page URL in an ad so it hard-codes a query string.** Overrides the tracking template. Remove the hard-coded query string, rely on the template.
- **Launching personalization before verifying parameters reach the page.** Results will be garbage.
- **Picking creative-level personalization from day one.** Too many variants, too much review burden. Start at campaign-level.
- **Assuming Beta = unstable.** It's Beta, but functional. Do set expectations that behavior or UI may shift.

## Permission etiquette

This integration touches the user's Google Ads account (a live ad spend account) *and* their Dalton account.

- Tracking template changes are account-wide. State what you're changing, get confirmation, save.
- Google Ads OAuth: let the user complete it themselves.
- Landing page selection and personalization variants — always review before launch. Nothing goes live without explicit user approval.

## What *not* to do

- **Don't edit the user's ad copy or bid strategy.** Not your job.
- **Don't skip the tracking-template verification step** (Step 2).
- **Don't hard-code parameter values for the user** — Google's placeholders (`{campaignid}`, etc.) auto-fill with the real IDs. Never write a literal `campaign_id=12345`.
- **Don't promise specific Quality Score or ROAS improvements.** You can cite the mechanism ("consistent message-match helps Quality Score") but not specific numbers.

## When to hand off to another skill

- Landing page isn't installed with Dalton → `dalton-script-install`
- User wants to compare paid-traffic lift in GA4 → `dalton-google-analytics`
- User also wants Meta Ads personalization → `dalton-meta-ads` (different tracking setup — per-ad URL, not template)
- User wants to tweak the generated variants → `dalton-editing-experiments`
- Integration shows connected but variants aren't serving → `dalton-help`

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `_drafts/ad-personalization.md` — full ad personalization walkthrough (note: draft status, but canonical content for this feature)
- `_drafts/personalization-setup.md` — tracking template specifics for Google Ads
- `_drafts/personalization.md` — personalization flow deep-dive
- `faq/integrations.md` — integrations FAQ