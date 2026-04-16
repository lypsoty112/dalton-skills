# Dalton prompting — best practices

Shared reference for every Dalton skill that involves generating or modifying variants via the AI editor. Linked from `creating-experiments`, `editing-experiments`, and `dalton-help`.

The AI is strong when prompts are specific, positive, and single-change. It is brittle when prompts are vague, negative, or compound. Most "prompt not working" tickets are rewriting problems, not product problems.

## The four golden rules

### Rule 1 — be specific

| Vague | Specific |
|---|---|
| "Make it better" | "Focus on the time-saving benefit" |
| "Make it shorter" | "Make this one sentence" |
| "Change the layout" | "Move the reviews section above the product description" |
| "Add something here" | "Add a trust badge showing free returns" |
| "Improve the CTA" | "Make the button copy action-oriented, starting with a verb" |

### Rule 2 — positive instructions, not negative

| Instead of | Write |
|---|---|
| "Don't make it too long" | "Keep this under 10 words" |
| "Remove the fluff" | "Focus only on the main benefit" |
| "Don't be too salesy" | "Use a conversational, helpful tone" |
| "Avoid jargon" | "Use simple language a beginner would understand" |

### Rule 3 — device-specific when it matters

Add `on mobile only`, `on desktop only`, or `for mobile` to scope a change to one viewport. Examples:

- "Center this text on mobile only"
- "Make the button full-width on mobile"
- "Show a shorter headline on mobile only"

Even when a change is device-scoped, **always preview both viewports** — AI can unintentionally touch the other view.

### Rule 4 — one change per prompt

Compound prompts fail. Break them up:

**Instead of:**
> "Reorder these three sections, change the headline to focus on speed, and make the CTA more urgent"

**Do this as three variants:**
1. "Move the testimonials section above the features"
2. "Change the headline to emphasize how fast setup is"
3. "Make the CTA button copy more urgent"

## Prompts that work well

**Headlines:**
- "Make the headline focus on [specific benefit]"
- "Test a question-based headline"
- "Make this headline more specific about [outcome]"

**CTAs:**
- "Start the button text with an action verb"
- "Add urgency to the CTA without being pushy"
- "Make the button copy benefit-focused instead of action-focused"

**Layout:**
- "Move [element] above [other element]"
- "Add a [trust badge / social proof / urgency indicator] near the CTA"
- "Make the [section] more prominent"

**Copy:**
- "Shorten this to one sentence"
- "Add a specific number or statistic"
- "Focus on [pain point] instead of [feature]"

## Prompts that don't work (editor limits)

Some things are outside what the editor can do — no amount of prompting fixes these:

- **Dynamic content:** "Change the price" / "Update the inventory count"
- **Images:** "Add a product photo" / "Change the hero image"
- **Navigation:** "Rearrange the menu items"
- **Forms:** "Add a new form field"
- **Animations:** "Make this fade in"
- **Elements inside iframes or shadow DOM**

For these, use a URL Split Test (→ `dalton-split-tests`) with a completely different built-out page.

## Fast rewrites for vague user prompts

When a user gives a vague re-prompt, rewrite before running it through the editor:

- "Make it better" → ask *what specifically is wrong?*
- "Shorter" → "Cut to one sentence" or "Under 10 words"
- "More urgent" → "Add 'ends Friday' to the CTA button" or "Change 'Learn more' to 'Start today'"
- "Fix mobile" → "On mobile only, [specific fix]"
- "More on-brand" → check Brand Intelligence settings (often an upstream fix is better than a new prompt)
- "Less corporate" → "Use a conversational tone — contractions are fine"

## When prompts repeatedly fail

Two or three attempts on the same element with no usable output usually means one of:

1. **Element is dynamic** — price, inventory, size selector, logged-in user data. The AI can't reliably target these.
2. **Element is inside an iframe or shadow DOM.** Check with View Source; if it is, select a parent in the main document instead.
3. **Element loads after initial HTML** (heavy SPA behavior). Try a parent element, or if the element genuinely can't be targeted, use a URL Split Test.
4. **Prompt is still too compound or too vague.** Strip to one instruction, rewrite as specific.

If none of the above resolves it, route to `dalton-help`.

## Upstream fixes > repeated prompting

When the same type of issue recurs — variants that drift off-brand, copy that over-promises, repeated policy violations — the fix is in **Brand Intelligence**, not in every new prompt. Add the brand voice instruction or restricted word there once, then re-prompt normally.

## Canonical sources

- `~/Documents/dalton/dalton-docs/editor/prompting-best-practices.md` — original doc
- `~/Documents/dalton/dalton-docs/editor/creation.md` — creation modes
- `~/Documents/dalton/dalton-docs/editor/brand-intelligence.md` — upstream fixes
- `~/Documents/dalton/dalton-docs/faq/troubleshooting.md` — when prompts fail
