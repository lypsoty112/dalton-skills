# Dalton glossary (full)

Full term list. Load this when a user asks about a specific term not covered in the SKILL.md quick glossary, or when precise definitions matter.

## A

**A/B Test** — Comparing two versions of a page to see which performs better. Dalton uses multi-armed bandits instead of classical A/B, but the goal is the same.

## B

**Baseline** — The original page without Dalton modifications. By default 20% of traffic is held on baseline to measure overall Dalton impact. Adjustable in settings.

**Brand Context** — Settings tab where brand voice, restricted words, and compliance requirements live. Auto-prefilled from a full website scan; editable by the user. The AI reads this when generating variants.

## C

**Certainty** — 0–100% confidence that a variant performs differently from baseline. 95%+ is the threshold for decision-making.

**Conversion** — The desired visitor action (purchase, signup, form submit, click). What Dalton optimizes for.

**Conversion Goal** — The specific action chosen per experiment. Dalton supports button clicks, form submits, page views, and custom GA4 events.

**Conversion Rate** — `(conversions / visitors) × 100`.

## E

**Experiment** — A test on a page with one or more variants. Dalton distributes traffic and measures automatically.

## H

**High Performer** — A variant that has reached statistical significance and outperforms baseline. Green indicator in the dashboard.

## M

**Multi-Armed Bandit (MAB)** — The algorithm Dalton uses. Instead of fixed 50/50, it dynamically shifts traffic toward winners while still exploring. Dalton uses Thompson Sampling specifically.

**Multi-Page Experiment** — An experiment running on multiple URLs at once. Changes apply to all matching pages.

## P

**Prompt** — The instruction to the AI to generate or modify content. Specificity matters: "Keep under 10 words, emphasize free shipping" beats "make it better."

## S

**Statistical Significance** — The point at which results are unlikely due to random chance. Dalton shows this as a "certainty" percentage. 95%+ is the conventional threshold.

## T

**Traffic Allocation** — How Dalton splits visitors between baseline and variants. Auto-adjusted by the algorithm based on performance.

## U

**URL Split Test** — A test comparing entirely different page URLs (for major redesigns or elements Dalton can't modify directly), rather than element-level changes on one page.

## V

**Variant** — A modified version of the page being tested against baseline. An experiment can have many variants.

**Visitor** — A unique person. Dalton tracks visitors (not page views) for accurate experiment data.
