---
name: copying-experiments
description: Duplicate a Dalton experiment across multiple pages — either by copying a variant from one page to another, or by setting up a multi-page experiment with a URL wildcard so the same test runs on many pages at once (all PDPs, all category pages, all blog posts, etc.). Use this skill whenever someone asks to copy, duplicate, clone, replicate, or "apply this test everywhere" / "run on all product pages" / "use the same variant on page X". With browser automation, drive the multi-page flow at platform.getdalton.com; without it, coach them through wildcard patterns and the critical "elements must exist on all matched pages" rule.
---

# Copying Dalton experiments

Two very different tasks live under "copy":

1. **Multi-page experiment** — one experiment targets many URLs via a wildcard pattern, so the same element change applies to every matching page at once. This is the supported flow.
2. **One-off copy from page A to page B** — at time of writing, there is no UI button for duplicating a variant between individual pages. The fallback is to recreate the experiment on the target page using the same prompt.

Know which case the user is in before choosing how to help.

## The editor URL

```
https://platform.getdalton.com/admin/[customer_id]/page?page=[page_id]&tab=overview&edit=true
```

Multi-page experiments live on a page entry that represents the *pattern*, not a single URL. When you set up a wildcard target, the resulting `[page_id]` covers the whole pattern. Full UI map: `../../references/platform-urls.md`.

## Which case is this?

| User says | Which case |
|---|---|
| "Run this same test on all my product pages" / "apply to all PDPs" / "use a wildcard" / "match all blog posts" | **Multi-page experiment** |
| "Same headline test on the pricing page and the homepage" (two specific unrelated URLs) | **Manual re-create** (no wildcard fits) |
| "Copy this variant to page X" (single target) | **Manual re-create** (UI copy not available) |
| "I need to test 3 completely different redesigns against each other" | Not copying — route to `dalton-split-tests` |

## Case 1 — multi-page experiment

The supported, native flow. One experiment, one URL pattern, same element change applies to every matched URL.

### When multi-page fits

- Product detail pages — test "Add to Cart" button copy across every product
- Category pages — test a trust badge in the same position on every category listing
- Blog posts — test a CTA above the footer across every article
- Landing pages — campaign pages with the same structure

### The critical rule

**Only test elements that exist identically on every matched page.** This is the #1 failure mode.

The common mistake: the user is on a product page and tests the product title or price. But those elements are different on every product page. A multi-page experiment applies the test *version* everywhere — suddenly all products have the same title.

| Works well (identical across pages) | Fails (page-specific) |
|---|---|
| "Add to Cart" button text | Product titles |
| "Free shipping" banner | Product prices |
| Review section header | Product descriptions |
| Trust badges near checkout | Inventory / size selectors |
| Generic category heading | Category-specific content |

If what the user wants to test is page-specific, they need single-page experiments instead (one per page), not a multi-page experiment.

### URL targeting rules (verified 2026-04-16)

Multi-page experiments use a **URL Targeting Rules** builder, not bare wildcard strings. Entry point: dashboard → **Add Page** → **Multiple Pages** tab → "Create multi-page" dialog.

Two ways to define the rules:

**Option A — natural language (fastest):** Type a plain-English description in the "Describe which pages to target" field (e.g. *"all product pages on artoftea.com"*) and click **Generate rules**. Dalton's AI builds the ruleset. You can then refine it.

**Option B — manual conditions:** Click **Add conditions manually**. Set the **Domain** (required), then add one or more **Path** conditions:

| Matcher | Use for |
|---|---|
| `start with` | All pages under a path prefix — e.g. `/products/` |
| `contain` | Path contains a string — e.g. `/collection/` |
| `end with` | Path suffix — e.g. `/details` |
| `match pattern` | Wildcard syntax — e.g. `/shop/*/view` |
| `match regex` | Complex patterns |
| `match exactly` | A single specific path |
| `equal (ignoring query)` | Same path regardless of query string |

Combine conditions with **AND** (all must match) or **OR** (any must match). Use **Add group** for nested logic. The dialog has a live **"Test a URL"** field — paste example URLs to confirm they match before proceeding.

Narrow conditions enough that they only catch pages where the test element genuinely exists. Over-broad rules are a common bug source (e.g. `/` matches everything).

### Operator flow

1. **Confirm what the user wants to test is page-layout-identical, not page-specific.** Ask. If they're unsure, list 3–5 URLs that would match and verify the target element exists and looks the same on each.
2. **Define the targeting rules.** Use the natural language input if the pattern is simple. For complex cases (exclude a subdirectory, combine two path prefixes) use manual conditions. Test example URLs in the live test field before proceeding.
3. **Navigate to the editor URL.** If the multi-page ruleset already exists as a page entry, open it directly. If not, add it via Add Page → Multiple Pages.
4. **Select the target element.** Ideally use the visual editor on a representative matched URL. Avoid fragile selectors (build-hashed classes); prefer IDs, `aria-label`, or text content.
5. **Create the variant** (→ hand off prompting details to `creating-experiments`).
6. **Preview on 3–5 different matching URLs.** This is non-negotiable. Open each in the browser tool and confirm: the element exists, the variant renders, desktop and mobile both look right.
7. **If any matched page looks broken or the element is missing**, narrow the pattern or pick a different element. Don't launch.
8. **Save. Launch only on explicit user confirmation.**

### Manual walkthrough (fallback)

If you can't drive the browser:

1. Decide URL pattern with wildcards.
2. User adds the multi-page experiment in the dashboard with that pattern.
3. User picks a common element.
4. User opens the preview for 3–5 different matching URLs and confirms each looks correct.
5. Launch.

## Case 2 — manual re-create across specific pages

Some scenarios don't fit a wildcard — e.g. the user wants the same test on the homepage and the pricing page (two unrelated URLs). There's no "duplicate to" button today.

The workaround:

1. On the source page, note down the **exact prompt** that produced the variant (or the Insight / template name).
2. Navigate to the target page's editor URL.
3. Create a new variant using the same prompt, element selection, and goal.
4. Preview both pages' variants and check they actually match in tone/content (AI output can drift).
5. Repeat for each target page.

This is labor-intensive; if the user has many target pages, push them toward multi-page with a wildcard pattern if their pages share a common structure.

### Flag the gap

The Dalton docs explicitly note: *"Can I copy experiments between pages? Not in the UI yet. Note your prompts to replicate them manually. Copy feature coming soon."* If a user expected a one-click copy, acknowledge the gap — don't pretend it works — and recommend the wildcard route if applicable.

## Common mistakes

- **Testing a page-specific element in a multi-page experiment.** Product titles, prices, category descriptions — these change per page. The test version takes over all of them. Always ask: "is this element identical on every page in the pattern?"
- **Over-broad URL pattern.** `example.com/*` matches *everything*, including checkout and admin pages. Narrow it.
- **Skipping the 3–5-URL preview.** The rule exists because "it looked fine on the first page" is how broken multi-page experiments ship.
- **Pretending the manual re-create is as good as a real copy.** Flag it as a workaround.
- **Using a wildcard when two unrelated URLs would do.** If you can name the two URLs individually, multi-page may be overkill — just run two single-page experiments with the same prompt.

## Permission etiquette

Multi-page experiments touch many live pages at once. Before launch:

- Tell the user which URL pattern you're about to deploy to.
- List 3–5 example URLs it will affect — make sure they agree those are all intended.
- Confirm the element choice is pattern-safe (identical across pages).
- Get explicit launch confirmation.

## What *not* to do

- **Don't launch without previewing 3–5 matched URLs** — every time.
- **Don't let users test a product-specific element in a multi-page experiment.** Push back.
- **Don't use a super-broad wildcard** without examining what else it catches.
- **Don't claim "copy experiment" is a one-click feature** — it isn't.
- **Don't launch.** User decides.

## When to hand off to another skill

- User hasn't created the source experiment yet → `creating-experiments`
- User wants to tweak the copy after duplicating → `editing-experiments`
- User actually wants to compare different full-page designs, not duplicate one test → `dalton-split-tests`
- Something breaks on matched pages → `dalton-help`
- User wants to set / change the shared conversion goal → `dalton-page-goals`

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `advanced/multi-page-experiments.md` — the full multi-page walkthrough including wildcard syntax and the "elements must match" rule
- `getting-started/multi-page-experiments.md` — getting-started version
- `editor/creation.md` — editor creation flow
- `faq/editor.md` — explicitly notes "copy experiments between pages" is not yet in the UI
