# Dalton platform URL patterns & UI map

Shared reference for every skill that drives `platform.getdalton.com` via browser automation. State observed 2026-04-16 on a live customer account. If an agent lands somewhere unexpected, share the URL with the user rather than guessing — URL structures can shift.

## Two identifiers drive every URL

- **`customer_id`** — a long numeric ID (example: `1725629676404`). Exposed in the URL path after login. Read it there. Never fabricate.
- **`page_id`** — a UUID (example: `649e871d-c95a-4504-9450-0388b08404b9`). Read it from the URL after clicking a page in the Pages list, or after creating one via "Add Page..." / "Optimize Single Page".

## Top-level URLs

| Section | URL |
|---|---|
| Platform root | `https://platform.getdalton.com` |
| Post-login landing (Pages list) | `/admin/[customer_id]/overview` |
| Brand Intelligence | `/admin/[customer_id]/brand` |
| Split Tests (list) | `/admin/[customer_id]/traffic-split` |
| Split Tests (create) | `/admin/[customer_id]/traffic-split/new` |
| Integrations | `/admin/[customer_id]/integrations` |
| Docs (external) | `https://docs.getdalton.com` |

## Per-page URLs

Page editor URL template:

```
https://platform.getdalton.com/admin/[customer_id]/page?page=[page_id]&tab=[tab]&edit=[true|false]
```

Valid `tab` values:

- `tab=overview` — default. Shows metrics (Conversions or Bounce Rate, with Daily/Weekly/Monthly toggle).
- `tab=data` — "Live Experiments" tab; lists experiments with statuses (Live / Paused / Draft).

`edit=true` enters the **Visual Editor** overlay on top of whatever tab is selected.

## Page Settings panel (important)

On any page view, a **gear icon** next to the "Visual Editor" button opens a **Page Settings side panel** containing:

- **Script Installed** status badge (green = installed on this page)
- **Optimization Goal** — edit inline (pencil icon)
- **Secondary Metrics** — pick a secondary metric to monitor alongside the primary (UI is live; not just roadmap)
- **Page Tags** — custom tags for filtering in the Pages list
- **Holdout Traffic** — shows the current baseline holdout %. **Warning in the UI:** "Changes will affect all pages" — it's a global setting even though the control lives on a per-page panel. **Default is 20%** (80% optimized). Do not adjust this on behalf of a user.

This panel is the quickest operator path for: editing the goal, confirming install status per-page.

## Install snippet — it lives on Integrations

The snippet is **inside the Integrations page**, not a separate URL. Open the "View Instructions" button on the "Install Dalton Script for AB Testing & Tracking" panel. A modal opens with:

- A "Share with Developer" shortcut (top-right of modal)
- Platform tabs: **JavaScript, Shopify, Webflow, Wix, Elementor, React, Next.js, NuxtJS, Framer**
- The snippet itself, per-account, with a `Copy` button
- **Installation Options** side-panel: **Antiflickering** and **Reapply Changes** toggles
- Explicit UI warning: *"Don't use GTM to install, this will introduce flickering on your website."*

Snippet details (for agents that need to identify the install programmatically):

- Preconnect hints to `cdn.getdalton.com` and `track.getdalton.com`
- `<script id="dalton-installation-script">` block sets `DALTON_ID` = the numeric customer_id
- Loads the SDK script from `https://cdn.getdalton.com/<customer_id>.min.js` with script id `dalton-sdk`

CSP allowlist domains: `cdn.getdalton.com`, `track.getdalton.com`.

## Visual Editor UI reference

Inside `tab=overview&edit=true`:

- **Top bar:** "Exit editor" (left) · desktop/mobile viewport toggle (center) · "Need help?" (right)
- **Left panel:** Experiments list for this page. Each entry shows title, author ("by Dalton" / "by [user]"), and status: **Live**, **Paused**, or **Draft**.
- **Right panel:** Live-rendered page with edit overlay.
- **Bottom-left prompt box:** placeholder is **"What would you like to test?"**. Next to it: an "Actions" button, an "Add context element" button (select an element on the page to scope the prompt), and a "Send prompt" submit button.

## User menu (top-right avatar)

- Logged in as [email]
- Testing Mode / Testing Account indicators (for Dalton-internal usage)
- Export Demo Data / Import Demo Data (Dalton-internal demo tooling)
- New customer account
- Customer Overview
- **Manage Users** — team/user management
- Log out

## Brand Intelligence page — section map

The Brand Intelligence page is richer than public docs suggest. Sections:

- **Brand Description** — auto-generated, editable
- **Industry** — single tag
- **Keywords** — multi-tag
- **Important Benefits** — editable bullet list
- **Remarks & Guidelines** — editable free-text field; this is where brand rules live (e.g. "Don't mention competitors directly"). There is no separate "restricted words" UI — put those in Remarks & Guidelines.
- **Brand Assets** — upload files or add URLs to assets (including review links); included as context in experiments
- **Competitors** — explicit list with "Add competitor" button
- **Brand Style** — auto-extracted screenshot of the brand + color palette swatches (top-right card)

## Integrations page — map

The Integrations page contains cards for each integration:

- **Install Dalton Script for AB Testing & Tracking** — "View Instructions" button (opens install modal)
- **Google Analytics** — Connected/Disconnected. Benefits: "Use goals and events from Google Analytics." Buttons: "Manage Accounts" / "Disconnect".
- **Google Ads** — Connected/Disconnected. **Two** benefits: "Use keywords to generate better variants" + "Allow for ad-to-page personalization." Button: "Connect Google Ads".
- **Meta Ads** — Connected/Disconnected. **Two** benefits: "Use creatives to generate better variants" + "Allow for ad-to-page personalization." Button: "Connect Meta Ads".
- **TikTok Ads** — status "Coming Soon".
- **Twitter Ads** — status "Coming Soon".

## Split Tests setup — 4-step wizard

Creating a URL Split Test at `/admin/[customer_id]/traffic-split/new` walks through 4 steps:

1. **Name** — "Experiment Name* — Give your experiment a descriptive name"
2. **URLs** — add the URLs to route traffic across
3. **Conversion Goal** — the shared goal across all URLs
4. **Traffic Split** — pick the mode (Fixed / Smart / Aggressive per public docs; confirm UI labels when setting up)

Each step has a Next button.

## Rules when navigating

- Respect auth walls — never try to bypass login, 2FA, or SSO. Let the user sign in.
- After login, read the `customer_id` out of the URL path. State it back to the user in case they have multiple accounts.
- Navigate by URL when possible (faster and more reliable than clicking through the sidebar).
- If a click or URL lands somewhere unexpected, share what you see with the user; don't push on.
- The settings gear on a page view is the single most productive starting point for operator flows — it surfaces install status, goal, holdout, and tags in one panel.
