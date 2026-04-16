---
name: editing-experiments
description: Revise an existing Dalton variant or experiment — re-prompt, tweak copy, fix a broken mobile layout, undo unwanted changes, adjust an approved variant before launch, or iterate on a generated variant that isn't quite right. Use this skill whenever someone wants to change, edit, tweak, adjust, fix, refine, or iterate on an existing variant rather than create a brand new one. Also use when the user says "that's not quite right, make it X" after a variant was generated. With browser automation, open the variant in the editor at platform.getdalton.com and apply the change; without it, coach them through the iteration loop.
---

# Editing Dalton experiments

Use this skill to iterate on a variant that already exists. Different problem than creation: the element is already selected, a version has been generated, and the user wants it *different*. The trap here is stacking too much change in a single prompt — this skill pushes discipline.

## The editor URL

```
https://platform.getdalton.com/admin/[customer_id]/page?page=[page_id]&tab=overview&edit=true
```

Navigate here to open the variant in edit mode. The left-panel **Experiments** list shows every experiment on the page with its status (**Live**, **Paused**, **Draft**) — click the one to revise. Full UI map: `../../references/platform-urls.md`.

## The iteration loop

Editing a variant is a tight cycle:

1. **Identify what's wrong** — specific, not "it's not great"
2. **Write a targeted prompt** — one change, specific, positive phrasing
3. **Re-generate**
4. **Preview desktop + mobile**
5. **Compare to previous version** — did it actually improve?
6. Repeat if still off, or **undo / delete and re-prompt** if the variant went sideways

The most common mistake is re-prompting with compounded changes ("make it shorter, more urgent, and center it on mobile"). Break it into separate edits.

## Default path: operator flow

Assuming browser automation:

### Step 1 — open the variant

1. Navigate to the editor URL.
2. Locate the variant in the list. Click to open it in edit mode.
3. Preview the current state on desktop and mobile so you and the user share the same baseline.

### Step 2 — diagnose what's wrong

Ask the user (or deduce from the conversation) which of these applies — the right next step depends on it:

| User says | What to do |
|---|---|
| "It's too long" | Re-prompt with a length constraint: "Cut to one sentence" / "Under 10 words" |
| "Tone's wrong" | Re-prompt with tone direction: "Make it more conversational" / "Drop the exclamation mark" — and consider adding a tone note to Brand Intelligence |
| "Mobile is broken" | Re-prompt with `on mobile only`: "On mobile only, make the hero headline two lines max" |
| "Wrong fact / claim" | Re-prompt with correct wording: "Replace 'free shipping on all orders' with 'free shipping over $50'" — flag that Brand Intelligence compliance rules could prevent this next time |
| "Wrong element got changed" | Undo → re-select the correct element → re-prompt |
| "It's fine but I want to try a different angle" | Keep existing variant, **create a new one** (→ `creating-experiments`) so you can A/B the angles |
| "Totally off, start over" | Delete the variant, start a fresh prompt (→ `creating-experiments`) |

### Step 3 — re-prompt

Apply the four golden prompting rules (specific / positive / device-scoped / one change per prompt). Full guide in `../../references/prompting-best-practices.md`. The editing-specific cheat sheet below is for the common vague re-prompts users send.

Generate.

### Step 4 — preview + compare

1. Open the new variant on desktop and mobile.
2. Put it side-by-side with the previous version mentally — ask: did this prompt actually change what I wanted, without breaking anything else?
3. If yes, save. If not, go back to Step 2 or 3.

### Step 5 — save, don't launch

Save the revised variant. Launching is a separate step requiring explicit user confirmation — this skill stops at "variant is in a good state."

## Manual walkthrough (fallback)

If you can't drive the browser:

1. Tell the user to open the editor URL (give them the pattern; they fill in their IDs).
2. Help them write the re-prompt — specific, positive, one change.
3. Ask them to preview desktop and mobile and report back.
4. If the new version still isn't right, iterate on the prompt together before they re-run it.

## Undo vs delete vs re-prompt — picking the right move

- **Undo** — use when the last single change was wrong and reverting gets you back to a good state. Cheap, no loss of prior work.
- **Delete and re-prompt** — use when the variant has drifted so far from the user's goal that iterating isn't worth it. Fresh start, with a better prompt.
- **Re-prompt (keep variant)** — use when the variant is mostly right but needs one targeted change. Most common case.

## Prompt rewriting cheat sheet

When the user's re-prompt is vague, rewrite it before running:

- "Make it better" → "[what's actually wrong?]" — push back for specifics
- "Shorter" → "Cut to one sentence" or "Under 10 words"
- "More urgent" → "Add 'ends Friday' to the CTA button" or "Change 'Learn more' to 'Start today'"
- "Fix mobile" → "On mobile only, [specific fix]"
- "More on-brand" → Add brand voice note to Brand Intelligence, then re-prompt with the usual intent
- "Less corporate" → "Use a conversational tone — contractions are fine"

## Common iteration traps

- **Compound prompts.** "Shorten, change tone, and center on mobile" — split into three edits.
- **Re-prompting a dynamic element.** If the element is a price, inventory count, or a size selector, no prompt will work. Route to `creating-experiments` (change element) or `dalton-split-tests`.
- **Drifting off-brand repeatedly.** The fix is upstream, in Brand Intelligence, not in every new prompt.
- **Ignoring mobile.** Re-prompted a desktop fix and declared success without checking mobile.
- **Using negative prompts.** "Don't do X" works worse than "Do Y". Always rewrite.

## Permission etiquette

- Tell the user what prompt you're about to run before you run it.
- After each iteration, show both viewports — don't assume success from a save.
- Never launch as part of an edit. Launch is explicit.

## What *not* to do

- **Don't re-prompt a dynamic element.** It won't take.
- **Don't stack four changes into one re-prompt.** Split them.
- **Don't treat the AI as read-my-mind.** If the user says "make it better", push for what "better" means before prompting.
- **Don't skip mobile preview** after a desktop-scoped edit.
- **Don't launch** — same rule as creating.

## When to hand off to another skill

- User wants a fundamentally different variant, not a revision → `creating-experiments`
- User wants to duplicate the (revised) variant across other pages → `copying-experiments`
- Edit is doomed because the underlying page needs to be a different page → `dalton-split-tests`
- Prompt attempts keep failing or the element won't target → `dalton-help`
- Variant is ready to go live → `dalton-setup` Phase 4 / user-initiated launch

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `editor/prompting-best-practices.md` — the full prompting guide (iteration section especially)
- `editor/creation.md` — where creation differs from editing
- `editor/brand-intelligence.md` — upstream fix for recurring brand/tone issues
- `faq/editor.md` — editor FAQ including "can I undo" and preview behavior
- `faq/troubleshooting.md` — when prompts fail repeatedly
