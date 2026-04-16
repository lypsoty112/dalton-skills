---
name: results-interpretation
description: Interpret Dalton's Results Dashboard for a user — what lift, certainty, traffic split, learning phase, and High Performer status actually mean, and whether to wait, act, pause, or launch a follow-up. Use this skill when data is *flowing* but the user isn't sure what to do with it — "is this variant winning?", "are these results significant?", "why is the split still 50/50?", "should I stop?", "how long until I can trust the numbers?", "what does this lift mean?". If data is NOT flowing at all (zero sessions, dashboard empty, conversion goal silent), that's a setup/install problem — route to `dalton-help`. With browser automation, open the dashboard and read the numbers for them; without it, apply the three-phase + 95%-certainty rule to the numbers they report.
---

# Interpreting Dalton results

Use this skill to turn dashboard numbers into a decision. The user's implicit question is almost always "should I act on this?" — and the answer depends on where the experiment sits in its lifecycle (still in learning phase vs. past it vs. converged).

## The results dashboard at a glance

Dalton surfaces data on two tabs:

**Overview tab** — high-level performance across all experiments:

- **Live Page Combinations** — variants currently running
- **Conversion Rate (30D)** — overall conversion rate across all pages
- **Conversion Lift vs Baseline** — total Dalton uplift (80% Optimized vs 20% Control holdout)
- **Sessions Analysed** — total tracked sessions

**All Experiments tab** — per-variant detail:

| Column | Meaning |
|---|---|
| COPY | Variant text being tested |
| SPLIT | Current traffic allocation (MAB-determined) |
| SESSIONS | Sessions assigned to this variant |
| CVR LIFT | Conversion-rate change vs baseline |
| SIGNIF | Statistical significance / certainty indicator |

**The two numbers that drive decisions are CVR LIFT and SIGNIF.** Everything else is context.

## The three phases of an experiment's life

Dalton's algorithm (Thompson Sampling — Bayesian multi-armed bandit) moves through three phases. Knowing which phase a variant is in tells you how much to trust the numbers.

| Phase | Timing | Traffic split example | What to do |
|---|---|---|---|
| **Exploration** | Week 1–2 | Control 33% / A 33% / B 34% | Don't act. Numbers swing wildly. Algorithm is still gathering. |
| **Exploitation** | Week 2+ | Control 15% / A 65% / B 20% | Still don't act yet. Algorithm is shifting toward winners — trend is informative, numbers aren't final. |
| **Convergence** | Week 4+ | Control 10% / A 80% / B 10% | Results are stable. Now you can act. |

If a user is looking at week-1 numbers and asking "should I declare a winner?", the answer is **no** — not because the numbers might be wrong, but because the algorithm hasn't finished working.

## The learning phase (a different thing than "exploration")

Before Dalton can report meaningful lift *at all*, it needs:

- **~250 sessions**, **and**
- **~100 conversion events**

Below these thresholds, traffic is split roughly equally and numbers fluctuate a lot — variants might show +50% one day and −10% the next. That's not a bug; it's insufficient data.

How fast users hit these thresholds depends entirely on traffic volume:

- **10K+ sessions/month per page** — thresholds hit in days, directional results in 2–4 weeks
- **5K–10K/month** — 6–8 weeks
- **<5K/month** — Dalton isn't a great fit. Flag this plainly.

If the user asks "why don't I have results yet?" — check their session/conversion counts first. The answer is usually "you're not out of learning phase yet."

## The 95% rule

For decisions that matter — declaring a winner, rolling a variant to 100%, killing the losers — wait for **95%+ certainty**. Dalton marks variants that cross this threshold as **High Performers** (green indicator in the dashboard).

Below 95% certainty, the numbers are suggestive, not conclusive. They're fine for rough trend-watching; they're not fine for permanent changes.

**Don't soften the rule under deadline pressure.** If a user needs to decide in week 2 of a test and the numbers aren't significant yet, the honest answer is "we don't know yet." Acting on noise is how trust gets lost.

## Default path: read the dashboard for them

Operator flow, assuming browser automation and the user being logged into `platform.getdalton.com`.

### Step 1 — open the dashboard

Navigate to the page in Dalton. Use `tab=overview` for the metrics summary or `tab=data` to see the per-experiment list. Full URL pattern: `https://platform.getdalton.com/admin/[customer_id]/page?page=[page_id]&tab=overview`.

### Step 2 — read the three things that actually matter

1. **Is the experiment out of the learning phase?** Look at the session + conversion counts. Below ~250 sessions or ~100 conversions → *wait, too early to say anything*.
2. **What's the current traffic split?** If it's still near-even, algorithm is in exploration — trend is informative, nothing is final. If one variant has 60%+ traffic, algorithm is exploiting — trust the trend more.
3. **Any variant at 95%+ certainty (High Performer)?** If yes, that's a real result. If no, keep waiting.

### Step 3 — translate for the user

Give them one of four honest answers:

- **"Too early."** Under learning-phase thresholds. Come back in N days when traffic should cross 250 sessions and 100 conversions.
- **"Algorithm's still exploring."** Learning phase done, but certainty hasn't hit 95% on any variant. Trend is informative; don't act on it.
- **"Variant X is winning, but not conclusively."** One variant has meaningful lift but certainty is under 95%. Wait or continue.
- **"Variant X is a High Performer."** 95%+ certainty, confirmed lift. Can act on it — declare winner, roll out, launch the next experiment.

### Step 4 — tell them the next step

- If "too early" or "still exploring" → wait; don't touch anything; don't stop the experiment just because early numbers look bad.
- If there's a confirmed winner → user's call. Options: keep running (learn more), roll the winner to 100%, launch a new experiment based on what worked.
- If *all* variants are underperforming baseline at high certainty → acceptable to stop. Losses revert instantly, no penalty.

## Manual walkthrough (fallback)

If you can't drive the browser:

1. Ask the user to check: session count, conversion count, current traffic split, and any High Performer indicators.
2. Apply the decision logic above based on what they report.
3. Give them one of the four answers plus a next step.

## When to pause vs keep running

Users often want to pause experiments that "look bad." Resist this.

**Pause for:**

- Urgent bugs on the live site unrelated to the test
- Content errors in the variant (wrong claim, broken link) — fix and relaunch
- Brand/PR issues (variant copy that got sensitive)

**Don't pause for:**

- Underperformance. The algorithm is already shifting traffic away from losers — let it.
- Anxiety. Early numbers are noise. Waiting is cheap; deciding on noise is not.

Stopping an experiment is instant and reverts changes with no penalty — communicate that so users feel safe pausing when they should, and comfortable leaving things running when they shouldn't.

## How to pause or resume an experiment (operator flow)

1. Navigate to the page: `https://platform.getdalton.com/admin/[customer_id]/page?page=[page_id]&tab=data`
2. The **Live Experiments** tab lists experiments with statuses (Live / Paused / Draft).
3. On each experiment, the status pill toggles — click to pause a Live experiment or resume a Paused one. (The Visual Editor's left panel also lists experiments with the same controls.)
4. Changes revert instantly; no penalty.

Get explicit user confirmation before pausing anything — the user may not want traffic to revert even if they said "numbers look bad."

## Why numbers don't match GA4

Common confusion: Dalton dashboard and GA4 show different absolute numbers. That's expected and fine:

- Different attribution windows
- Different sampling
- Different bot handling

**The relative comparison (variant vs baseline) is what matters — and that's consistent.** Don't let a user invalidate a real result because Dalton says 5.2% CVR and GA4 says 4.9%.

## Common interpretation mistakes

- **Reacting to week-1 numbers.** Noise.
- **Comparing Dalton's absolute CVR to GA4's absolute CVR and panicking at the gap.** Different methodologies.
- **"One variant is way below baseline, I should stop."** The algorithm is already shifting traffic away. Let it work.
- **"Certainty is 90%, close enough."** No. 95% or wait.
- **Ignoring session-count context.** A 30% lift at 100 sessions means nothing. A 6% lift at 10,000 sessions is real.
- **Stopping to "save the data."** You can't — experiment data isn't data *about* your site, it's data about that specific experiment. Stopping invalidates the conclusion.

## Permission etiquette

Reading results is read-only — low stakes. But:

- Don't declare a winner or kill a variant for the user. Present the read, give the recommended next step, let them decide.
- If the user wants you to pause or stop an experiment, confirm the reason matches the "pause for" list above. If it's anxiety-driven, gently recommend waiting.

## What *not* to do

- **Don't invent lift numbers or certainty values.** Read them; don't guess.
- **Don't declare a winner below 95%.** Even if the user wants you to.
- **Don't advise pausing because numbers look bad early.** The algorithm handles that.
- **Don't treat learning-phase fluctuations as signal.** They're not.
- **Don't adjust Holdout Traffic.** It's an account-wide setting and not something to touch on behalf of a user.

## When to hand off to another skill

- Dashboard shows zero data / issues with data flow → `dalton-help`
- Numbers don't match GA4 and user wants to dig in → `google-analytics`
- User wants to launch a follow-up experiment based on winning variant → `creating-experiments`
- User wants to roll the winner to 100% traffic → this isn't a user-adjustable setting; explain that Dalton's algorithm shifts traffic automatically
- Privacy/data-storage questions mid-conversation → `privacy-and-storage`

## Canonical sources

Paths under `~/Documents/dalton/dalton-docs/`:

- `optimization/results-dashboard.md` — dashboard walkthrough, learning phase definition
- `optimization/how-the-algorithm-works.md` — MAB phases, Thompson Sampling, when NOT to use MAB
- `faq/results.md` — results FAQ including GA4 discrepancy explanation
- `optimization/conversion-goals.md` — what's being measured
