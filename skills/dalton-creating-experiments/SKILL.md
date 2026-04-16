---
name: dalton-creating-experiments
description: Generate a new variant in the Dalton editor — via accepting an AI Insight, picking a template from the library, or writing a custom prompt. Use this skill when the specific task is *generating the variant itself* — clicking Insights, using the template library, writing the prompt, previewing the result. Trigger on "create a variant", "write a prompt", "accept this insight", "use a template", "add a new element / banner / badge", "change this headline / CTA". This skill does NOT cover the setup sequence (→ `dalton-setup` if they're onboarding end-to-end), conversion goal selection (→ `dalton-page-goals`), or revising an already-generated variant (→ `dalton-editing-experiments`).
---

# Creating Dalton experiments

Use this skill to produce new variants. The job has three entry points (Insight / template / prompt) and one shared output: a variant the user reviews and approves before launch.

**You don't need to wait for one experiment to finish generating before starting another.** The editor supports creating multiple experiments in parallel — start a second prompt while the first is still rendering, or open a second page's editor in a new tab. Each experiment generates independently.

## The editor URL

```
https://platform.getdalton.com/admin/[customer_id]/page?page=[page_id]&tab=overview&edit=true
```

Use this for every operator-mode step below. If you don't know the IDs, go to `https://platform.getdalton.com`, pick the page from the Pages list, and read them out of the URL. Full UI map: `../references/platform-urls.md`.

## The prompt box and Actions button

When the Visual Editor is open, the bottom-left has:

- A prompt textbox with placeholder **"What would you like to test?"** — type here to generate.
- **Actions** button — opens creation modes (Insights, templates, and other creation shortcuts).
- **Add context element** button — click an element on the page to scope the prompt to it.
- **Send prompt** (submit) button.

Use the Actions button when the user wants Insights or a template; use the prompt box directly for custom prompts; use "Add context element" whenever the prompt refers to a specific on-page element.

## The three ways to create a variant

| Entry point | Best for | What the agent does |
|---|---|---|
| **Accept an Insight** | First-time users, easy wins, when the user's asked "what should I test?" | Pick the highest ICE-score Insight and one-click accept |
| **Pick a template** | When the user has a type of test in mind (trust badge, urgency banner, USP row) but no strong opinion on execution | Browse the template library, choose the closest match, let AI apply it |
| **Custom prompt** | When the user has a specific hypothesis or wants a particular element modified | Click the element, write a specific prompt, generate |

Insights and templates are faster and usually better for new users. Custom prompts shine when the user knows exactly what they want — they're also the right fallback when Insights/templates don't cover the idea.

## Default path: drive the editor

Operator flow, assuming browser automation and the user being logged in.

### Step 1 — open the editor

Navigate to the editor URL for the target page. Confirm you're on `tab=overview&edit=true`. The page should render with the Dalton edit overlay active.

If the page doesn't have a `page_id` yet — because it hasn't been added to Dalton — ask the user to click **Optimize Single Page** first (dashboard → Optimize Single Page → paste URL → Dalton runs a CRO scan).

### Step 2 — decide entry point with the user

- Ask what they want to test, in one sentence.
- If they say "not sure, suggest something" → **Insights**. Accept the top-ICE-score idea.
- If they name a pattern ("add a trust badge", "put urgency near the CTA") → **templates**. Open the template library, pick the closest match.
- If they describe a specific edit ("rewrite this headline to emphasize free shipping") → **custom prompt** on the selected element.

### Step 3a — accept an Insight

1. In the editor, open the **Insights** panel.
2. Read Insights to the user with their ICE scores (Impact / Confidence / Ease). For a first experiment, recommend the highest total score with Ease ≥ a reasonable threshold.
3. Accept it. AI generates the variant.
4. Jump to Step 4 (preview + review).

### Step 3b — pick a template

1. Open the template library from the editor.
2. Browse templates; each has a short description of the testing pattern.
3. Select the one that matches the user's intent. AI adapts the template to the page using Brand Intelligence (brand voice, colors, layout).
4. Jump to Step 4.

### Step 3c — custom prompt

1. **Click the element** in the visual editor that the user wants to change, OR use the prompt box for a page-level addition (e.g. "add a testimonials section above the pricing table").
2. **Write the prompt following the golden rules** (see below). If the user's initial prompt is vague, rewrite it on their behalf and confirm — don't feed "make it better" to the AI and hope.
3. Generate. AI returns a variant.
4. Jump to Step 4.

### Step 4 — preview, review, save

1. Preview the variant on **desktop**. Check that the change renders and nothing on the page looks broken.
2. Preview on **mobile**. This is where most surprises live — the AI optimizes for whichever viewport was active when the prompt ran.
3. Read the generated content for **accuracy** (claims, numbers), **brand fit** (tone matches), and **compliance** (no misleading promises).
4. If the variant is off, re-prompt (→ `dalton-editing-experiments`) or delete and try a different entry point.
5. Save. Do not launch yet — launch is the pre-launch QA skill's job (`dalton-setup` Phase 4 or the user's explicit "launch it").

## The golden prompting rules (summary)

Four rules underpin every good prompt. Condensed version — full guide with tables, examples, and fallback recipes is in `../references/prompting-best-practices.md`.

1. **Be specific.** "Make it shorter" → "Make this one sentence."
2. **Positive instructions, not negative.** "Don't be salesy" → "Use a conversational, helpful tone."
3. **Device-scope when relevant.** Add `on mobile only` / `on desktop only` — but always preview both viewports.
4. **One change per prompt.** Three changes = three variants.

If the user's initial prompt is vague, rewrite it before feeding it to the editor. "Make it better" in, bland copy out.

## Multi-page experiments — check before you build

If this experiment needs to run across multiple pages (all PDPs, all blog posts, etc.), read `dalton-copying-experiments` **before** building the variant. The critical constraint: **the element you're testing must exist identically on every matched page.** Product titles, prices, and page-specific content fail this check — they change per page and the variant would overwrite them all. Build the variant only after confirming the element is pattern-safe.

## What can't be tested

Some things are outside the editor's reach: dynamic content (prices, inventory), images, videos, navigation menus, forms, animations, elements inside iframes or shadow DOM. For any of those, route to `dalton-split-tests`. See `../references/prompting-best-practices.md` for the full "prompts that don't work" list and the fallback recipe.

## Brand Intelligence

Every variant generated by the AI reads from **Brand Intelligence** (accessible from the editor). Auto-populated from a full website scan. The user can add:

- Brand voice / tone instructions
- Restricted words (never use)
- Compliance rules (e.g. "never claim guaranteed results")
- Color / style preferences

If variants consistently come out off-brand or over-promising, tell the user to add guardrails in Brand Intelligence rather than re-prompting every time. One upstream fix > many downstream corrections.

## When prompts keep failing

See the "When prompts repeatedly fail" section in `../references/prompting-best-practices.md` — covers dynamic elements, iframe / shadow DOM, parent-element fallback, and prompt simplification.

If those don't resolve it, route to `dalton-help`.

## Permission etiquette

- Before generating, tell the user what prompt (or Insight / template) you're about to run.
- After generating, show desktop and mobile previews — don't declare success just because the save succeeded.
- Never launch. Save only. Launch is a separate step the user must explicitly authorize.

## What *not* to do

- **Don't feed vague prompts to the AI.** Rewrite for specificity first, even if that means pushing back on the user.
- **Don't accept a variant without previewing mobile.** Mobile is the most common surprise.
- **Don't stack 4 changes into one prompt.** Split them; one change per variant.
- **Don't re-prompt instead of fixing Brand Intelligence** for recurring brand/compliance issues.
- **Don't launch.** Not your call — user decides.

## When to hand off to another skill

- User wants to revise an already-generated variant → `dalton-editing-experiments`
- User wants to duplicate this variant to other pages → `dalton-copying-experiments`
- Change requires a whole different page (images, heavy redesign) → `dalton-split-tests`
- Pre-launch QA and actual launch → `dalton-setup` Phase 4
- Something is failing or the element won't target → `dalton-help`

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `editor/creation.md` — the three creation entry points
- `editor/prompting-best-practices.md` — the full prompting guide
- `editor/ai-suggestions.md` — Insights, ICE scoring, what the CRO scan does
- `editor/brand-intelligence.md` — Brand Intelligence setup
- `experiments.md` — experiment types (text, HTML, section order, insertions)
- `faq/editor.md` — editor FAQ
