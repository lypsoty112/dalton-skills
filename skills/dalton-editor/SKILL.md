---
name: dalton-editor
description: Work in the Dalton Visual Editor — create, edit, or copy experiments and variants. Use this skill as the entry point whenever someone wants to build, modify, duplicate, re-prompt, or iterate on a Dalton experiment. Routes to the right sub-skill: dalton-creating-experiments (new variants, AI prompts, templates, Actions menu), dalton-editing-experiments (revise existing variants), dalton-copying-experiments (duplicate across pages / multi-page). Also covers the editor's core concepts: the Visual Editor URL, the "What would you like to test?" prompt box, experiment statuses (Live / Paused / Draft), Brand Intelligence, can/can't test.
---

# Dalton editor (family root)

Router for the editor skill family. The editor is where variants are built — between "page has Dalton installed" and "experiment is live."

## The editor URL

Every editor sub-skill drives this URL pattern:

```
https://platform.getdalton.com/admin/[customer_id]/page?page=[page_id]&tab=overview&edit=true
```

- `[customer_id]` — numeric ID in the URL path after login.
- `[page_id]` — UUID, in the URL after clicking a page from the Pages list.
- `tab=overview` is the default; `tab=data` shows the Live Experiments list instead.
- `edit=true` overlays the Visual Editor on top of whichever tab is selected.

If you don't have the IDs, go to `https://platform.getdalton.com`, pick the page from the Pages list, and read both IDs out of the resulting URL. Full URL map: `../references/platform-urls.md`.

## The Visual Editor UI

When `edit=true`:

- **Top bar:** "Exit editor" (left) · desktop/mobile viewport toggle (center) · "Need help?" (right).
- **Left panel:** "Experiments" list for this page. Each entry shows title, author ("by Dalton" / "by [user]"), and status — **Live**, **Paused**, or **Draft**.
- **Right panel:** the live-rendered page with edit overlay active.
- **Bottom-left prompt box:** placeholder is **"What would you like to test?"**. Adjacent:
  - **Actions** button — opens additional creation modes (Insights, templates, etc.).
  - **Add context element** button — click an element on the page to scope the prompt to it.
  - **Send prompt** (submit) button.

## Which sub-skill to use

| User is doing | Use |
|---|---|
| Building a **new** variant from scratch — accepting an Insight, using a template, writing a prompt | `dalton-creating-experiments` |
| Iterating on an **existing** variant — re-prompting, tweaking copy, fixing mobile | `dalton-editing-experiments` |
| **Duplicating** an experiment across pages or via a URL wildcard (PDPs, category pages, blog posts) | `dalton-copying-experiments` |
| Comparing **completely different page designs** (full redesign) | `dalton-split-tests` — NOT an editor skill |

If the request straddles two sub-skills ("copy this across all PDPs and tweak the CTA on mobile"), handle in two phases: copy first, then edit.

## Core editor concepts (shared across sub-skills)

- **Insights / CRO scan.** AI-generated experiment ideas scored Impact / Confidence / Ease. One-click accept turns each into a variant.
- **Templates.** Library of 25+ pre-built testing patterns the AI adapts to the page.
- **Prompting.** Two modes: click an element and prompt on it, or prompt a new variant from scratch. Guide in `../references/prompting-best-practices.md`.
- **Brand Intelligence.** Brand voice, color palette, keywords, competitors, and rules at `/admin/[customer_id]/brand`. All generated variants respect it.
- **Preview.** Desktop / mobile viewport toggle in the top bar; switch to confirm variants render on both before approval.

## What can and can't be tested in the editor

**Can:** headlines, body text, CTAs / button copy, section order, new elements (badges, banners, social proof), layout via prompts, spacing.

**Can't:** images, videos, navigation menus, dynamic content (prices, inventory, per-user personalization), forms (adding/removing fields), animations, elements inside iframes or shadow DOM.

For the "can't" list, route to `dalton-split-tests` (full alternate page) or recommend the user change the element selection.

## The golden prompting rule

**Be specific. One change per prompt. Use positive instructions.** Full guide in `../references/prompting-best-practices.md`. Most-common failure: users writing "make it better" and getting bland output — push for specificity early.

## Creating multiple experiments in parallel

You don't need to wait for one experiment to finish generating before starting another. The editor supports creating experiments in parallel — start a second prompt while the first is still rendering, or open a second page's editor in a new tab. Each experiment generates independently.

## Experiment statuses

Three statuses appear in the Experiments list:

- **Draft** — created but never launched. Safe to edit, delete, preview.
- **Paused** — was live, now stopped (instant revert). Can be resumed or edited.
- **Live** — currently running and serving variants to traffic.

Only Live experiments feed the Results dashboard. Draft and Paused contribute no data.

## Permission etiquette

Editor output eventually goes live. Always:

- State what variant you're about to generate or change.
- Get explicit user confirmation.
- Preview desktop AND mobile before the user commits.
- Never click Launch without the user explicitly saying so.

## When to hand off to another skill

- User needs the script installed first → `dalton-script-install`
- User needs to set / change the conversion goal → `dalton-page-goals`
- Full-page vs full-page comparison → `dalton-split-tests`
- Something's broken or stuck → `dalton-help`
- User asks about results, significance, learning phase → `dalton-results-interpretation`

## Canonical sources

- Platform URL map: `../references/platform-urls.md`
- Prompting guide: `../references/prompting-best-practices.md`
- Docs: `editor/creation.md`, `editor/prompting-best-practices.md`, `editor/ai-suggestions.md`, `editor/brand-intelligence.md`, `experiments.md`, `faq/editor.md` under `~/Documents/dalton/dalton-docs/`
