---
name: dalton-script-install
description: Install (or fix the install of) the Dalton JavaScript snippet on a user's website. With browser automation, fetch the snippet from the Integrations page at platform.getdalton.com, apply it in their site builder or repo, and verify it loads on the live page. Without browser access, coach them through it. Use this skill whenever someone asks how to install Dalton, where the script goes, how to share the install with a developer, how to verify the install, sees "Installation required" on the dashboard, or asks platform-specific install questions (Shopify, Webflow, Wix, Elementor, React, Next.js, NuxtJS, Framer, WordPress, headless, GTM, custom HTML). Also covers the two install toggles (Antiflickering, Reapply Changes).
---

# Dalton script installation

Use this skill to get a user's site installed with Dalton. The install is one `<script>` tag in `<head>`. With browser automation you can perform the whole flow yourself; without it, coach the user.

## What installation actually does

One snippet unlocks all of these at once:

- Visitor tracking (anonymous — no PII)
- Experiment delivery on any page
- URL split-test routing across multiple variant pages
- Automatic integration with any cookie-consent banner (OneTrust etc.)
- Automatic GA4 event tracking (a `dalton` event with a `type` dimension of `control` or `optimized`)

No backend changes, no build-step changes, no SDK. Just one `<script>` tag.

## Where the snippet lives on platform.getdalton.com

The install snippet is **inside a modal on the Integrations page** — not at a standalone "Settings → Installation" URL. Open the modal by navigating to:

```
https://platform.getdalton.com/admin/[customer_id]/integrations
```

…then clicking **View Instructions** on the **"Install Dalton Script for AB Testing & Tracking"** panel.

The modal has:
- A **Share with Developer** shortcut (top-right) — generates a login-free link the developer can act on directly.
- Platform tabs: **JavaScript, Shopify, Webflow, Wix, Elementor, React, Next.js, NuxtJS, Framer** (verified 2026-04-16 — re-verify if platforms change).
- The account-specific snippet with a **Copy** button.
- An **Installation Options** side panel with two toggles (see below).
- An explicit UI warning: *"Don't use GTM to install, this will introduce flickering on your website."*

If the user's platform isn't in that tab list (WordPress, Squarespace, Angular, headless, etc.), use the **JavaScript** tab — it's the generic instructions.

## The two installation options (toggles)

Both live in the install modal's **Installation Options** side panel:

- **Antiflickering** — prevents page flicker during experiment loading. Small perf cost, much cleaner UX. Recommend turning on for any page users will see.
- **Reapply Changes** — auto-reapply experiments on DOM updates. Important for SPAs or dynamic sites where content re-renders after initial load (React/Next/Vue/Nuxt/Angular). Recommend turning on whenever the site is a JS framework SPA.

Mention these proactively when the user is on a SPA or complains about flicker. They're easy to miss.

## Default path: do it for the user

Assume browser automation and possibly filesystem access to the user's site repo.

### Step 1 — fetch the snippet from the Integrations page

1. Confirm the user is signed in. Don't bypass auth, 2FA, or SSO — user drives login.
2. Navigate to `https://platform.getdalton.com/admin/[customer_id]/integrations`. If `customer_id` is unknown, go to the platform root after login and read it from the URL path.
3. Click **View Instructions** on the install panel.
4. Pick the right platform tab for the user's site.
5. Read the snippet verbatim (use the Copy button or read it out of the DOM). Don't edit, don't regenerate, don't guess.
6. Consider enabling **Antiflickering** and, for SPAs, **Reapply Changes**. Confirm with the user before flipping either toggle — they're account-level.

If you genuinely can't reach the snippet (auth wall, locked account), stop and ask the user to paste it in. Never fabricate the snippet — it carries a per-account `DALTON_ID`.

### Step 2 — figure out where it goes

Ask the user what platform the site runs on, or infer from the URL (`/wp-admin` → WordPress, a repo with `next.config.js` → Next.js, etc.). Then apply the snippet to the right location:

| Platform | Where to paste |
|---|---|
| **Shopify** | Admin → Online Store → Themes → Edit code → `layout/theme.liquid` → inside `<head>` |
| **Webflow** | Project Settings → Custom Code → **Head Code** → publish |
| **Framer** | Site Settings → General → **Custom Code → Start of `<head>` tag** → publish |
| **Wix** | Settings → Custom Code → Add Custom Code → place in **Head** |
| **Elementor (WordPress)** | Elementor → Site Settings → Custom Code, OR a head-snippet plugin |
| **WordPress (general)** | Theme `header.php` inside `<head>`, or a head-snippet plugin (e.g. *Insert Headers and Footers*) |
| **Squarespace** | Settings → Advanced → Code Injection → **Header** |
| **WooCommerce / Magento / BigCommerce** | Theme settings or head-snippet plugin; paste in the site-wide `<head>` |
| **Next.js (App Router)** | `app/layout.tsx` — inside the root layout's `<head>`, via `<Script>` or a raw `<script>` tag |
| **Next.js (Pages Router)** | `pages/_document.tsx` — inside `<Head>` |
| **React (CRA / Vite)** | `public/index.html` → inside `<head>` |
| **Vue / Nuxt** | `nuxt.config.ts` → `app.head.script`, or `index.html` for Vite-SPA Vue |
| **Angular** | `src/index.html` → inside `<head>` |
| **Headless CMS / custom** | Wherever the site-wide `<head>` template lives |
| **Google Tag Manager** | Possible, but Dalton's own UI explicitly warns against GTM — it causes flicker. Use only as an absolute last resort. |

Two rules that cover 90% of install problems:
- Snippet goes in **`<head>`**, not `<body>`. In `<body>` it can still load but experiments flicker or miss.
- Place it **as high in `<head>` as possible**, before other third-party scripts.

Scope: default to **sitewide** (site-wide `<head>` template) unless the user explicitly wants specific pages only.

### Step 3 — apply the snippet

Before making any change, tell the user what you're about to do and get confirmation — this is a modification to a live site or their repo.

- **Filesystem access to the repo** (Next/React/Vue/Angular/headless/custom): edit the right file and add the snippet inside `<head>`. Do not add it twice. Don't run their build unless asked.
- **Browser access to a hosted builder** (Shopify theme editor, Webflow, Framer, Squarespace, Wix, WordPress admin): navigate to the Head Code field, paste, save/publish.
- **Neither (separate developer owns the code):** use **Share with Developer** from the install modal. Send them the generated link. Cleaner than pasting into Slack or email.

### Step 4 — verify

Two checks:

**A. Load the live page and confirm the script fires.** Open the URL in your browser tool. Check any of:
- A network request to `cdn.getdalton.com` (the SDK load)
- The `<script id="dalton-installation-script">` block in the raw HTML
- A `dalton` namespace on `window` (evaluate `typeof window.dalton !== 'undefined'`)

**B. Confirm on Dalton's side.** From the page view on `platform.getdalton.com`, open the **settings gear** (top-right of the page view, next to "Visual Editor"). The top of the **Page Settings** panel shows a green **"Script Installed"** badge if Dalton detects the install.

Tell the user both checks passed (or which didn't) and what you're going to do about it.

## Manual walkthrough (fallback)

If you don't have browser automation:

1. Tell the user to go to `platform.getdalton.com` → **Integrations** → **View Instructions**.
2. Have them pick the platform tab and copy the snippet.
3. Paste into the site-wide `<head>` template using the table above.
4. Recommend turning on **Antiflickering**, and for SPAs **Reapply Changes**.
5. If they don't own the code, use **Share with Developer**.
6. Verify from the page view's settings gear — it should show **Script Installed**.

## Permission etiquette

The agent is about to modify a live site or third-party account. Before doing so:

- State plainly what you're about to do. ("I'll open your Shopify admin, edit `theme.liquid` to add the Dalton snippet inside `<head>`, and save.")
- Wait for explicit confirmation.
- After applying, tell the user what actually changed, in one sentence.
- For the Antiflickering / Reapply Changes toggles, explicitly confirm before flipping — they're account-level settings.

If auth, 2FA, or payment walls block you, stop and hand back control. Don't fake or try bypasses.

## When installation doesn't "take"

- Snippet is in `<head>`, not `<body>`.
- Pasted into the **live/published** template, not a draft that wasn't published.
- Caching layers (Cloudflare, Shopify CDN, WordPress caching plugins) cleared after paste. Hard-reload or incognito to bypass browser cache.
- Site's Content Security Policy allows `cdn.getdalton.com` and `track.getdalton.com`. Strict CSPs need these two domains allowlisted.
- No conflicting testing tool (VWO, Optimizely, Google Optimize) on the same pages.
- You're looking at the Page Settings gear, not a stale dashboard.

If none of this resolves it, route to `dalton-help`.

## What *not* to do

- **Don't invent or guess the snippet.** It has a per-account `DALTON_ID`; only the one from the user's Integrations page works.
- **Don't edit the snippet.** It's served as-is. Modifying it breaks verification and experiment delivery.
- **Don't put it in `<body>`.** "It loads" ≠ "it works cleanly."
- **Don't layer Dalton on top of another A/B tool** on the same pages. Migrate off the other tool first.
- **Don't push GTM.** Dalton's UI explicitly warns against it (flicker). Only if no other option.
- **Don't modify their live site** without explicit confirmation for the specific change.
- **Don't fake auth** or assume stored credentials.

## When to hand off to another skill

- User wants the big picture before installing → `dalton-info`
- Installed the snippet and now wants their first experiment → `dalton-setup` or the `dalton-editor` family
- User wants to define the conversion goal for a page → `dalton-page-goals`
- Install is fine but something else is broken → `dalton-help`
- GA4 audiences / ads integration wiring after install → the `dalton-integrations` family

## Canonical sources

- Platform URL map & UI: `skills/references/platform-urls.md`
- Docs: `getting-started/installation.md`, `integration.md`, `getting-started/platform-guides.md`, `faq/setup.md`, `faq/troubleshooting.md` under `~/Documents/dalton/dalton-docs/`
