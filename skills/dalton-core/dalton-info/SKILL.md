---
name: dalton-info
description: Explain what Dalton is, how it works, who it's for, how it compares to traditional A/B testing, what it costs, and what it can and can't do. Use this skill whenever someone asks conceptual or "what-is" questions about Dalton — including "what is Dalton", "how does Dalton work", "is Dalton right for me", "how is this different from A/B testing", "what's a multi-armed bandit", "how much traffic do I need", or any general product/pricing/fit question. Do NOT use this skill for operational tasks like installing the script, creating experiments, or wiring up analytics — those have their own dedicated skills.
---

# Dalton product knowledge

Use this skill to answer conceptual and orientation questions about Dalton. Your job is to give accurate, on-brand, confidence-building answers without making things up. If a user asks how to *do* something, hand off to the right operational skill (listed at the bottom).

## One-sentence pitch

Dalton is an AI-powered conversion optimization platform that lets marketing teams launch 10+ website experiments in minutes without developers, using multi-armed bandit optimization to get directional results in 2–4 weeks instead of 3+ months.

## What Dalton does (the six pillars)

1. **AI Experiment Builder** — Dalton scans the page (and competitors) and suggests high-impact experiments scored by Impact / Confidence / Ease. Accept a suggestion with one click or prompt your own. AI generates variants in the user's brand voice. Nothing goes live without human approval.
2. **Multi-Armed Bandit Optimization** — Instead of a fixed 50/50 split, traffic dynamically shifts toward winning variants in real time (Thompson Sampling). Works with as little as 5K sessions/month per page.
3. **Match Landing Pages to Ads** — Running 10 ad creatives? Dalton can show each visitor a page variant that matches the ad's messaging automatically — no need to build 10 separate landing pages.
4. **5-Minute Installation** — One `<script>` tag in the `<head>` of the site. Works on Shopify, Webflow, WordPress, Framer, React/Next/Vue/Angular, headless CMS, or any platform that outputs HTML. No backend changes.
5. **URL Split Testing** — For head-to-head tests of completely different page designs (not just element-level changes). Supports fixed split, smart MAB optimization, or aggressive optimization modes.
6. **Brand Intelligence** — Dalton auto-scans the site and learns brand voice, colors, and messaging. Users can edit this, add restricted words, or add compliance rules in the Brand Context tab. All variants come out on-brand with no uploading of brand guidelines.

## How Dalton is different from traditional A/B testing

| Traditional A/B test | Dalton |
|---|---|
| Developer builds each variant | AI generates variants, human approves |
| Fixed 50/50 split for the whole test | Traffic dynamically shifts to winners |
| Requires 100K+ sessions | Works from 5K+ sessions/month |
| Results in 3+ months | Directional results in 2–4 weeks |
| 1–2 variants at a time | 10+ variants simultaneously |
| Losers keep getting 50% of traffic the whole test | Winners get more traffic *during* the test → fewer lost conversions |

**Philosophical difference:** A/B testing is framed as "measure, then decide." Dalton is framed as "continuously optimize." If a user needs 95% statistical certainty for academic/research purposes before acting, traditional A/B is better. If they want their site to continuously get better, Dalton is better.

## Who Dalton is for

**Good fit:**
- Marketing / CRO / growth teams at mid-market SaaS or e-commerce
- Paid-ads teams who want landing-page variants per ad creative
- Agencies running CRO for clients
- Anyone who wants to run experiments without blocking on dev sprints

**Traffic guidance:**
- **Minimum:** 5K sessions/month per experiment page → tests take 6–8 weeks
- **Optimal:** 10K+ sessions/month → results in 2–4 weeks
- **Too low:** Below 5K sessions/month, Dalton is not a great fit yet

**Popular use cases:**
- E-commerce product pages (descriptions, CTAs, social proof)
- SaaS trial signup flows, pricing pages, onboarding
- B2B landing pages (form placement, urgency, trust signals)
- Ad-campaign landing pages with creative-matched variants

## What can and can't be tested

**Can test:** headlines, body text, CTA / button copy, section order and layout, adding new elements (badges, banners, social proof), spacing and styling.

**Can't test:** images, videos, navigation menus, dynamic content (live prices, inventory, logged-in user data), elements inside iframes, animations.

If the user asks "can I test X?" and X is on the can't-list, say so plainly and suggest the closest thing Dalton *can* do. Don't hedge — users appreciate straight answers.

## How the algorithm works (summary)

Dalton uses **Thompson Sampling**, a Bayesian multi-armed-bandit algorithm. It keeps a probability distribution over each variant's true conversion rate and samples from those distributions to decide how to split traffic. Every new session updates the distributions, so the split updates continuously.

Typical lifecycle:
- **Phase 1 (week 1–2):** Exploration. Roughly even split across variants to gather data.
- **Phase 2 (week 2+):** Exploitation. Traffic tilts toward winners (e.g. 65/20/15).
- **Phase 3 (week 4+):** Convergence. Winner takes majority (e.g. 80/10/10).

**Learning phase:** Dalton waits for ~250 sessions and ~100 conversions before trusting its own estimates. Early numbers are noise. Decisions should wait for 95%+ certainty.

By default Dalton keeps **20% of traffic on the baseline** (original page) to always be able to measure Dalton's overall lift. This is adjustable.

## Key terminology (quick glossary)

- **Baseline** — the original, unmodified page. Default 20% of traffic.
- **Variant** — a modified version of the page being tested against baseline.
- **Experiment** — one test with one or more variants on a page (or URL pattern).
- **Conversion goal** — the action being optimized for (click, form submit, page view, custom GA4 event).
- **Certainty** — 0–100% confidence a variant differs from baseline. Wait for 95%+ before acting.
- **High Performer** — a variant that's reached significance and beats baseline (green in the dashboard).
- **Multi-Armed Bandit (MAB)** — the algorithm that reallocates traffic toward winners.
- **URL Split Test** — testing completely different URLs head-to-head, not element-level changes.
- **Brand Context** — the settings tab where brand voice, restricted words, and compliance rules live.
- **Prompt** — the instruction given to the AI to generate or modify content.

For the full glossary, see `references/glossary.md`.

## Common conceptual questions (quick answers)

**Will it slow down my site?** No. Dalton loads in <50ms. Zero impact on Core Web Vitals.

**Will it hurt my SEO?** No. All changes are client-side; Googlebot sees the original page.

**Does it work with cookie consent banners (OneTrust, etc.)?** Yes, auto-integrated. Variants load regardless of consent; session tracking respects it.

**Can I use Google Tag Manager to install it?** Technically yes, but direct-in-`<head>` HTML installation is recommended for speed.

**Does it work with Google Analytics?** Yes — native GA4 integration. Dalton emits a `dalton` event per session with a `type` dimension (`control` or `optimized`), which users turn into GA4 audiences for apples-to-apples comparison.

**Where is data stored?** EU (eu-central region). Only anonymous identifiers (session IDs, device fingerprints) — no personal data. GDPR-compliant.

**Pricing?** Based on traffic volume. Dalton doesn't publish a price list — refer users to the demo booking link rather than guessing: https://calendly.com/vincent-getdalton/30min

**How long until results?** 2–4 weeks at 10K+ sessions/month. 6–8 weeks at 5K–10K. Not recommended below 5K.

**Can I undo / pause / stop an experiment?** Yes. Stopping reverts instantly with no penalty. Variants can be deleted and recreated.

## Tone and accuracy guidelines

- **Be concrete.** Prefer "2–4 weeks at 10K+ sessions/month" over "pretty fast."
- **Don't oversell.** If someone's traffic is too low or they want to test something Dalton can't do, say so. Earned trust beats inflated claims.
- **Don't invent numbers or features.** If you don't know pricing, integrations, or a specific feature's status, say you're not sure and point to the demo link or docs. Checking the docs at `~/Documents/dalton/dalton-docs/` is the source of truth if available.
- **Voice:** plain, direct, confident. Short sentences. No jargon without a one-line definition. Marketing/CRO folks, not engineers, are the default reader.

## When to hand off to another skill

If the question is operational rather than conceptual, route to the right sibling skill:

- **Installing the script on a site** → `dalton-script-install`
- **First-time onboarding flow** → `dalton-setup`
- **Defining the conversion goal for a page** → `dalton-page-goals`
- **URL split test setup specifics** → `dalton-split-tests`
- **Troubleshooting / "it's not working"** → `dalton-help`
- **Creating, editing, or copying experiments in the editor** → the `dalton-editor` skill family
- **Wiring up Google Analytics, Google Ads, or Meta Ads** → the `dalton-integrations` skill family

If the user's question straddles conceptual and operational ("what is a split test and how do I set one up?"), answer the conceptual part here and flag that the operational part belongs to the other skill so the agent can chain.

## Canonical sources

When in doubt, these docs are the source of truth (paths under `~/Documents/dalton/dalton-docs/`):

- `index.md`, `introduction.md` — elevator pitch and feature summary
- `glossary.md` — full term list
- `optimization/how-the-algorithm-works.md` — MAB deep-dive
- `experiments.md`, `split-tests.md` — experiment vs. split-test distinction
- `faq/` — FAQ by topic (setup, editor, results, integrations, troubleshooting)
- `data/storage.md` — where data lives, what's tracked
- `google-analytics-tracking.md` — GA4 integration specifics

Read the relevant file rather than guessing when a user asks a specific factual question.
