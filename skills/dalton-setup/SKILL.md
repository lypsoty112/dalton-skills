---
name: dalton-setup
description: Orchestrate end-to-end onboarding for a new Dalton user — install → goal → first experiment → pre-launch QA → launch. Use this skill whenever the user needs the *whole sequence* coordinated — "how do I set this up", "how do I onboard", "how do I launch my first experiment", "walk me through getting started", "I just signed up". This skill owns the sequence and hands off each phase to the narrower skill (`dalton-script-install`, `dalton-page-goals`, `dalton-creating-experiments`). If the user is in the middle of *one* phase and knows which (e.g. just configuring the goal, or just creating a variant), route directly to that skill instead.
---

# Dalton onboarding

Use this skill to take a brand-new user from "I just signed up" to "my first experiment is live and validated." It's the orchestrator — it delegates each phase to the tightly-scoped skills but owns the overall sequence and the go-live QA.

## The onboarding spine

Five phases. Don't skip or reorder; each depends on the previous.

0. **Get your bearings** — find the customer_id, find (or add) the target page in the dashboard, capture the page_id.
1. **Install the script** on at least one page where they want to experiment. (→ `dalton-script-install`)
2. **Set the conversion goal** for that page. (→ `dalton-page-goals`)
3. **Create the first experiment** — AI Insights, a template, or a prompt. (→ `dalton-creating-experiments` in `dalton-editor`)
4. **Run the pre-launch QA checklist** and launch.

Own the sequence. After each phase, confirm it's actually done (ideally by verifying with your browser tool, not just taking the user's word for it) before moving on.

## Phase 0 — Get your bearings

Before driving anything, the agent needs two identifiers: `customer_id` and `page_id`. These feed every URL, especially the editor URL `https://platform.getdalton.com/admin/[customer_id]/page?page=[page_id]&tab=overview&edit=true`.

Neither is given up front — read them from the dashboard URL after login. Full URL map in `skills/references/platform-urls.md`.

### Discovering `customer_id`

1. Navigate to `https://platform.getdalton.com`.
2. If the user isn't signed in, ask them to sign in via your browser tool. **Never bypass** auth, 2FA, or SSO — the user drives login.
3. After login, the URL redirects to `https://platform.getdalton.com/admin/[customer_id]/overview` — a long numeric ID in the path. Read it.
4. Tell the user which customer_id you're operating on, in case they have multiple accounts.

### Discovering `page_id` — existing page

1. From `/admin/[customer_id]/overview` (the Pages list), locate the page by its public URL or name.
2. Click into it.
3. The URL becomes `/admin/[customer_id]/page?page=[page_id]&tab=overview` — `page_id` is a UUID. Capture it.

If the user has many pages and isn't sure which to start with, ask. Defaults: highest-traffic landing page, pricing page, primary signup/purchase page.

### Creating `page_id` — new page

1. From the Pages list, click **"Add Page..."** (or **"Optimize Single Page"** in some flows).
2. Paste the public URL of the target page.
3. Dalton runs a CRO scan. When it finishes, you land on a page-specific URL — `page=[page_id]` is the new ID.

### Checking install per-page

Once on a page view, click the **settings gear** icon (top-right, next to the "Visual Editor" button). The **Page Settings** panel shows a green **"Script Installed"** badge if Dalton detects the snippet on that page. If it says not installed, route to `dalton-script-install` before going further.

### Cold-start caveat

URL structures can change. Observe — read IDs from whatever URL appears after a known action (login, clicking a page, creating a page). If you land somewhere unexpected, share the URL with the user rather than pushing on.

## Default path: drive the whole flow for them

Assume you have browser automation. Here's the end-to-end operator script:

### Phase 1 — install

1. Confirm the user is logged into `platform.getdalton.com`.
2. Hand off to `dalton-script-install`. The install flow returns with a green "Installed" status on the Dalton dashboard.
3. Before advancing, **load the user's target page in the browser tool** and confirm a network request to a `dalton` domain fires. If it doesn't, resolve here — don't proceed with broken install.

### Phase 2 — pick the target page and conversion goal

1. Ask the user which page they want to optimize first. Good first candidates: highest-traffic landing page, primary pricing/signup page, key product page. Avoid low-traffic pages (<5K sessions/month).
2. In the Dalton dashboard, click **"Optimize Single Page"** and paste the URL. Dalton runs a CRO scan and generates **Insights** (AI-suggested experiments with ICE scores — Impact, Confidence, Ease).
3. Hand off to `dalton-page-goals` to set the conversion goal. Common defaults:
   - E-commerce: Add-to-cart button, or a purchase GA4 event
   - SaaS signup: Signup button click, or a `/thank-you` page view
   - B2B lead gen: Form-submit, or a `/demo-booked` page view
4. **Verify the goal will fire.** Have the user (or drive it yourself) perform the goal action once on the target page. The event should appear in the Dalton dashboard shortly after.

### Phase 3 — first experiment

1. Hand off to `dalton-creating-experiments` in the `dalton-editor` family.
2. For a first experiment, strongly recommend accepting **one** top-ICE-score Insight (not building from scratch). Easier to ship, and it gives the user an immediate "oh, that's neat" moment.
3. Keep it to **one variant** on top of baseline for the first test. Multiple variants is fine once they've seen the loop end-to-end.

### Phase 4 — pre-launch QA

Walk the Before-You-Go-Live checklist. Don't skip any item:

- [ ] **Desktop preview looks correct.** Load the preview URL in the browser tool. Verify the variant renders and nothing on the page looks broken.
- [ ] **Mobile preview looks correct.** Resize the viewport (or open in a mobile emulator) and check the variant on mobile. AI often optimizes for whichever view was active when the prompt ran — mobile is the usual source of surprises.
- [ ] **AI content is accurate.** Read every claim, number, and brand-sensitive phrase. If anything's inaccurate, have the user re-prompt before launch.
- [ ] **Conversion goal fires where Dalton is installed.** If the goal is a thank-you page view, the Dalton snippet must be on that thank-you page too.
- [ ] **(Multi-page only) Preview 3–5 URLs matching the pattern.** Handled by `dalton-creating-experiments` — but own the checkbox here.

Only after all five tick do you hit **Launch Experiments**.

### Phase 5 — first-week handoff

After launch, set the user's expectations — this prevents panicked "is it working?" messages on day 2:

- Traffic starts split roughly equally while Dalton's algorithm collects data.
- The learning phase needs ~250 sessions **and** ~100 conversions before Dalton's estimates stabilize. Below those thresholds, numbers swing — normal, not a bug.
- Directional results take **2–4 weeks at 10K+ sessions/month**, longer at lower volume.
- Look for **95%+ certainty** before making permanent decisions.
- They can pause or stop any experiment instantly — changes revert with no penalty.

## Manual walkthrough (fallback)

If you don't have browser automation, coach the user through the same five phases in the same order, handing off to the narrower skills (`dalton-script-install`, `dalton-page-goals`, `dalton-creating-experiments`) at each step. Still own the sequence: after each phase, ask the user to confirm done (ideally with a screenshot) before moving on.

## Permission etiquette

Onboarding touches a lot of things: their live site code, their Dalton account, sometimes their ad/analytics accounts. Before any phase:

- State plainly what you're about to do ("I'll paste the Dalton snippet into your Shopify theme's `<head>` and publish.")
- Wait for explicit confirmation.
- After doing it, tell them what actually changed, in one sentence.

Never launch an experiment live without the user explicitly saying "launch." The whole Dalton philosophy is "AI generates, human approves" — respect that.

## Common onboarding friction (and the fix)

- **"I don't have developer access."** Use Dalton's **Share with developer** button (`dalton-script-install` covers the flow). Generates a login-free link the dev can act on without a back-and-forth.
- **"We get 3K sessions a month on this page."** Below 5K, test cycles take 6–8 weeks instead of 2–4. Ask if there's a higher-traffic page to start with. Don't oversell — it's better to delay than launch an experiment that can't reach significance.
- **"Can we install in Google Tag Manager?"** Yes, but direct `<head>` install is preferred. Only use GTM if there's no other option.
- **"Can we test the product image?"** No. Dalton can't modify images. Suggest a URL Split Test (`dalton-split-tests`) if the user wants a full redesign comparison.
- **"I ran the install and the dashboard still says Installation required."** Cache. Hard-reload or check in incognito. Next most likely: the snippet was pasted into a draft template that wasn't published.

## What *not* to do

- **Don't skip pre-launch QA**, even if the user is in a hurry. A broken variant launched live erodes trust in Dalton immediately.
- **Don't create 4 variants on a first experiment.** One variant + baseline is enough to teach the loop and reach signal faster.
- **Don't set a goal on a page that doesn't have Dalton installed.** Thank-you pages especially — the snippet has to be there too.
- **Don't launch on a page below 5K sessions/month** without flagging that results will take 6–8 weeks.

## When to hand off to another skill

- Script install details and platform specifics → `dalton-script-install`
- Conversion goal types and GA4 event wiring → `dalton-page-goals`
- Variant creation, prompting, AI insights → `dalton-creating-experiments` (in `dalton-editor`)
- URL split tests for full redesigns → `dalton-split-tests`
- "What is Dalton?" conceptual questions mid-onboarding → `dalton-info`
- Onboarding is blocked by something breaking → `dalton-help`
- After launch, user wants to wire GA4 audiences for cross-report analysis → `dalton-google-analytics` (in `dalton-integrations`)

## Canonical sources

Source of truth (paths under `~/Documents/dalton/dalton-docs/`):

- `getting-started/first-experiment.md` — canonical first-experiment walkthrough
- `getting-started/installation.md` — install
- `getting-started/before-you-go-live.md` — pre-launch checklist
- `optimization/results-dashboard.md` — learning phase and when to trust results
- `editor/ai-suggestions.md` — Insights / CRO scan / ICE scoring
