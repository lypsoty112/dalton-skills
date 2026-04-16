---
name: dalton-page-goals
description: Choose and configure the conversion goal for a page or experiment in Dalton — pick among built-in goal types (CTR, click on element, click-to-path, add-to-cart, page view) or a GA4 event, set secondary metrics, then wire it up and verify it fires. Use this skill whenever someone is picking or changing what Dalton optimizes for — "what should I optimize for", "how do I set the conversion goal", "how do I track a form submit / signup / purchase", "my goal isn't firing", "how do I set a secondary metric". If they want to use a GA4 event but GA4 isn't connected, route to `dalton-google-analytics` first; this skill owns goal-type selection and Dalton-side configuration via the Page Settings panel.
---

# Dalton conversion goals

Use this skill to help a user decide what Dalton should optimize for on a given page, then configure it via the Page Settings panel. A wrong or unverified goal is the #1 cause of "why isn't my experiment producing data" tickets — take this phase seriously.

## Where goals are configured

Goals live in the **Page Settings** panel for each page. To open it:

1. Navigate to the page in Dalton: `https://platform.getdalton.com/admin/[customer_id]/page?page=[page_id]&tab=overview`
2. Click the **settings gear icon** at the top-right (next to the "Visual Editor" button).
3. The side panel shows **Optimization Goal** (edit with the pencil icon) and **Secondary Metrics**, plus install status, Page Tags, and Holdout Traffic.

The goal can also be edited inline from the page header: **Goal: [Type] [Edit]** link near the page URL.

## The four built-in goal types (+ GA4 events)

| Goal | Fires when | Best for |
|---|---|---|
| **Click-Through Rate (CTR)** | Any click on the page | Broad engagement when the user doesn't know which element matters yet |
| **Clicks on a specific element** | Visitor clicks a specific button/link | Most common — optimizing a primary CTA, signup button, add-to-cart |
| **Click to a specific path** | Visitor navigates to a specific URL | Primary CTA navigates internally (e.g. clicks that reach `/checkout`) |
| **Add-to-cart buttons** | Any add-to-cart action fires | E-commerce default — works across product pages |
| **Page views** | Visitor reaches a target URL path | Thank-you / post-purchase / confirmation pages. Requires Dalton installed on the target page too. |

**Plus: GA4 events.** With GA4 connected, any GA4 event can be the Dalton goal (purchases, custom signup, etc.). Often the most precise — lets Dalton optimize for downstream conversions instead of proxies.

## Secondary Metrics

Secondary Metrics are live in the Page Settings panel (not roadmap-only). Pick one or more to monitor alongside the primary — useful when the primary goal is a proxy (click) and you want visibility into a downstream action (purchase) without changing what the algorithm optimizes for.

**Example:** Primary = "Add to cart" click. Secondary = GA4 `purchase` event. Dalton optimizes for add-to-cart lift but you can see whether purchase lift tracks along with it.

Secondary metrics don't drive traffic allocation — only the primary goal does.

## Default path: configure the goal for them

Operator flow assuming browser automation.

### Step 1 — understand what the user cares about

Ask what the user actually wants to optimize. Not "which goal type" — "what visitor action matters for this page." Examples:

- E-commerce product page → "getting visitors to add to cart" → **Add-to-cart** goal, or the GA4 `add_to_cart` event if precision matters
- SaaS pricing page → "clicking Start Free Trial" → **Click on element** on that button, or **Page view** on `/signup-success`
- B2B landing page → "submitting the demo form" → GA4 `form_submit` event, or **Click-to-path** if the form redirects to a confirmation URL
- Blog post → usually not a great Dalton fit; ask what success even looks like

### Step 2 — pick the most-downstream reliably-measurable action

Downstream = closer to revenue / the real business outcome.

| Situation | Prefer |
|---|---|
| User has a GA4 purchase event | GA4 event > click goal |
| User clicks "Start Trial" → reaches `/signup-success` | Page view on `/signup-success` > the click |
| No downstream tracking exists | Click-on-element on the primary CTA |
| Multiple buttons matter about equally | CTR across the page |
| E-com store | Add-to-cart goal type (built-in) |

**Rule of thumb:** If the user has a GA4 event for the real action (purchase / signup), use it. Clicks are a proxy when you can't measure the real thing.

### Step 3 — open Page Settings and configure

1. Navigate to the page editor URL (`tab=overview`).
2. Click the settings gear → Page Settings panel opens.
3. Click the pencil next to **Optimization Goal**.
4. Configure:
   - **Click-on-element:** identify the element — ideally by clicking it in the visual selector, not a fragile CSS class. Prefer stable IDs, `aria-label`, or text content.
   - **Page view:** paste the exact URL path (e.g. `/thank-you`). Confirm Dalton is installed on that page too or the goal can't fire.
   - **Click-to-path:** paste the destination URL path.
   - **GA4 event:** select from the dropdown populated by the GA4 connection. If GA4 isn't connected, route to `dalton-google-analytics` first.
5. Optional — also set a **Secondary Metric** for visibility into a downstream signal.
6. Save.

### Step 4 — verify the goal will fire

Load the page in your browser tool and trigger the goal action once (click the button, submit the form, navigate to the URL). Check the Page Overview tab in Dalton for the event to appear (can take a minute or two). If it doesn't, diagnose before launching an experiment — don't launch with an unverified goal.

## Choosing between close alternatives

Two common close-calls:

- **Click-on-CTA vs. page-view on `/thank-you`:** prefer page-view if the thank-you page exists and Dalton is on it. More reliable (one event per actual conversion, not per misclick).
- **GA4 event vs. click goal:** prefer GA4 if the event reflects the real business outcome (purchase, qualified lead). Click is a proxy when GA4 isn't set up for it.

## Common mistakes

- **Goal on a page where Dalton isn't installed.** Especially thank-you pages. Page-view goals only fire if the snippet loaded on that URL.
- **Fragile selectors.** Class names like `css-a8b9d2` break on the next deploy. Prefer a button's ID, `aria-label`, or text content.
- **Wrong goal for the page.** Optimizing for CTR on a page with one primary CTA dilutes signal. Conversely, specific-element on a page where 5 buttons matter throws away data.
- **Launching without verifying.** Always trigger the goal action once and confirm it shows up in Dalton.
- **Changing the goal mid-experiment.** Invalidates collected data. Stop + restart if truly needed, and warn the user.

## Permission etiquette

Before changing or setting a goal:

- Tell the user which goal type you're about to configure and why.
- For click-on-element or page-view goals, confirm the exact selector / URL path first.
- After configuring, verify end-to-end and report.

## Tone

Users often pick a goal without thinking about whether it's measurable or downstream. Nudge toward the *most downstream reliably-measurable action*, but don't be pedantic — if a button click is their best option, that's fine.

## When to hand off to another skill

- Dalton snippet not installed on the goal page → `dalton-script-install`
- GA4 event as a goal but GA4 isn't connected → `dalton-google-analytics` in `dalton-integrations`
- Goal not firing after launch → `dalton-help`
- Choosing between a single-page experiment and a URL split test → `dalton-split-tests`

## Canonical sources

- Platform URL map: `skills/references/platform-urls.md`
- Docs: `optimization/conversion-goals.md`, `technical/analytics-integration.md`, `getting-started/before-you-go-live.md` under `~/Documents/dalton/dalton-docs/`
