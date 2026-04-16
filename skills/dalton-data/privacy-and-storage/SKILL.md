---
name: privacy-and-storage
description: Answer questions about Dalton's data handling — what data it collects, what it doesn't, where data is stored (EU / eu-central), how cookies work, GDPR/CCPA stance, the two cookies Dalton sets (dalton_session_[id], dalton_device), PII handling, and cookie-consent integration. Use this skill whenever someone asks about privacy, compliance, GDPR, CCPA, where data is stored, what Dalton tracks, PII, cookies, fingerprinting, data residency, the cookie banner, OneTrust integration, or whether Dalton is safe for EU users. Be precise and conservative — don't invent legal or compliance claims.
---

# Dalton privacy and data storage

Use this skill when a user asks about data, privacy, compliance, or cookies. These questions often come from the user's legal / IT / compliance team — answers need to be accurate, specific, and conservative. Never fabricate a claim that sounds reassuring but isn't in the docs.

## The short version (for quick answers)

- **Data is stored in the EU.** Primary zone: `eu-central`. All A/B testing operations happen in EU data centers.
- **No PII.** Dalton collects anonymous identifiers only — no names, emails, IP addresses, or identifying data.
- **Two cookies**, both first-party (set on the customer's domain, not Dalton's): `dalton_session_[id]` and `dalton_device`, each expiring after 100 days.
- **Respects cookie consent.** Dalton auto-integrates with most cookie banners (OneTrust and similar). Only when analytics consent is given are cookies stored. Variants still display regardless of consent; session tracking requires consent.
- **Domain-scoped.** Cookies are limited to the customer's domain — no cross-site tracking.
- **GDPR-compliant** by virtue of EU storage + no PII + consent integration.
- **Privacy questions / escalations:** `info@getdalton.com`. Full policy at `https://getdalton.com/privacy`.

## What Dalton DOES collect

From `data/cookies.md` and `technical/privacy-security.md`:

- **Anonymous identifiers** — random strings used to maintain test consistency across sessions
- **Experiment participation** — which tests a visitor is enrolled in
- **Variation assignment** — which version of a test the visitor sees
- **Conversion events** — anonymous goal completions and interactions
- **Click events** — anonymous clicks on the customer's pages
- **Scroll events** — tracked only on pages with live experiments

Everything is tied to the anonymous session/device identifiers. There's no link to the visitor's real identity.

## What Dalton DOES NOT collect

- **Personal information** — no names, emails, IP addresses, or identifying data
- **Cross-site tracking** — cookies scoped to the customer's domain only

When a user asks "does Dalton see PII?" the answer is no, and that's a firm statement backed by the docs. Don't hedge.

## The two cookies (exact details)

### `dalton_session_[id]`

- **Purpose:** anonymous session identifier for experiment consistency
- **Data stored:** random alphanumeric string
- **Expiration:** 100 days
- **Domain:** the customer's website domain (first-party)

Ensures a visitor sees the same test variation throughout their session, preventing inconsistent experiences that would skew test results.

### `dalton_device`

- **Purpose:** anonymous device identifier for consistent experiences across visits
- **Data stored:** random device fingerprint (random string — not a full browser-fingerprint technique that identifies the user)
- **Expiration:** 100 days
- **Domain:** the customer's website domain (first-party)

Ensures returning visitors continue seeing the same variation, so conversions are attributed correctly.

**Both cookies are classified as "essential for A/B testing functionality"** per Dalton's docs. Practically, this means they require analytics consent to be set — Dalton's consent integration handles that automatically.

## Data storage (residency)

- All A/B testing data is **processed and stored exclusively within the European Union**.
- Primary data zone: `eu-central`.
- Stored data includes: anonymous session IDs, device fingerprints, experiment participation records, conversion rates / goal completions, experiment content, and aggregate insights.

This is the key bullet point for EU customers, German customers especially, and anyone with strict data-residency requirements.

## Cookie-consent integration

- Dalton **auto-detects consent** from most cookie banners, OneTrust included, out of the box.
- **Only when analytics consent is given** are cookies set. Session storage (which holds the anonymous session ID during the current browser session) is used for seamless navigation within a session — but persistent cookies require consent.
- **Variants display regardless of consent**, but session tracking requires cookies — so visitors who reject cookies don't appear in the dashboard. This is the correct behavior (respects their choice); just call it out when users are confused about "missing" sessions.
- For cookie banners Dalton doesn't auto-integrate with, the customer can wire up consent manually — route to `dalton-help` or Intercom for specifics.

## GDPR and similar regulations

Dalton's architecture — EU storage + anonymous-only collection + consent-aware — aligns with GDPR and CCPA principles. The docs state compliance with both.

**Be specific, not hand-wavy.** When asked about a specific regulation, cite the concrete mechanisms (EU storage, no PII, consent integration) rather than generic "Dalton is compliant" claims. The user's legal team will ask why — have the why ready.

**If asked about specific certifications** (SOC 2, ISO 27001, HIPAA, specific sub-processor lists, DPA templates) and the docs don't cover it — say you don't know and route to `info@getdalton.com` or Intercom. Don't guess. Inventing a certification claim is the single worst thing an agent can do in a compliance conversation.

## Common privacy questions (scripted answers)

**"Does Dalton collect PII?"**
No. Dalton collects anonymous identifiers only — no names, emails, IP addresses, or identifying data.

**"Where is the data stored?"**
Exclusively in the European Union, in the `eu-central` zone.

**"Is Dalton GDPR compliant?"**
Yes. Data stays in the EU, no personal information is collected, and Dalton integrates with cookie-consent banners so visitors opt in before cookies are set.

**"What cookies does Dalton set?"**
Two first-party cookies on your domain: `dalton_session_[id]` for session consistency and `dalton_device` for returning-visitor consistency. Both expire after 100 days. Both store random strings only — no personal information.

**"Does Dalton do cross-site tracking?"**
No. Cookies are scoped to your domain only.

**"Does it work with OneTrust / our cookie banner?"**
Yes. Dalton auto-detects consent from OneTrust and most major cookie banners. Persistent cookies are only stored once analytics consent is given.

**"What happens if a visitor rejects cookies?"**
They still see experiment variants — but they won't be tracked in the Dalton dashboard, because session tracking requires cookies. This is by design (it respects the visitor's choice).

**"Do you have a DPA / DPIA template / sub-processor list / SOC 2 report?"**
Route to `info@getdalton.com` — those live outside the public docs.

## When to escalate to Dalton directly

Some privacy conversations should go to Dalton directly, not through an agent:

- Custom DPA / contract negotiations
- Specific sub-processor lists
- Certification reports (SOC 2, ISO 27001, etc.)
- Data-subject-access-request (DSAR) handling procedures
- Region-specific regulatory questions not covered in the docs

Route these to **`info@getdalton.com`** or Intercom on `platform.getdalton.com`.

## What *not* to do

- **Don't invent compliance certifications** or claim audits Dalton hasn't done.
- **Don't claim "Dalton is 100% compliant with [regulation]"** in absolute terms — regulations depend on how the customer uses Dalton too (e.g. whether they've wired their cookie banner correctly). Describe Dalton's mechanisms precisely, let the user's legal team draw the conclusion.
- **Don't speculate on IP address handling.** The docs explicitly say no IP addresses are collected — that's the claim, don't stretch it or hedge it.
- **Don't disclose sub-processors or infrastructure vendors** not already named in the docs.

## Tone

Legal-adjacent conversations deserve precision. Short, specific, factually-backed answers beat generic reassurance. If you don't know, say so and route — that's a better answer than a confident-sounding guess.

## When to hand off to another skill

- Dashboard "missing" sessions actually about cookie-rejection confusion → clarify here; deeper troubleshooting → `dalton-help`
- User wants to understand the control/optimized holdout split (a settings question, not a privacy question) → `results-interpretation` explains the default 20/80 split
- User asks about GA4 data handling (different system, different rules) → `google-analytics` for GA-specific questions; privacy of GA4 itself is Google's domain, not Dalton's

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `data/storage.md` — EU storage
- `data/cookies.md` — full cookie detail + collection principles
- `technical/privacy-security.md` — privacy overview incl. email `info@getdalton.com` and the public privacy policy URL
- `faq/integrations.md` — GDPR/CCPA FAQ statement

Public policy: `https://getdalton.com/privacy`
Privacy contact: `info@getdalton.com`
