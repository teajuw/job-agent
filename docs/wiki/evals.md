# Evals

> Skeleton. Applies [[demystifying-evals]] and [[hillclimbing]] to this project. The general method lives on those pages; this one records only what is specific here.

## What is being measured

Whether the judge gives the verdict Trevor would give. The judge is the app under test. The grader is code.

## A case

One requirement from one posting, judged against one version of the wiki.

| Field | Notes |
|---|---|
| `posting_id` | The split key |
| `requirement` | Text as extracted, frozen |
| `kind` | `required` or `preferred` |
| `wiki_commit` | The wiki version the label was given against |
| `label` | Trevor's verdict: yes / no / unclear |
| `why_hard` | Optional, one line. Present on every case added as a hard case. |
| `tags` | First tag is the grouping the report uses: proposal is the hard-call type from [[verdicts]] |

The runner checks out or reads the wiki at `wiki_commit`. When Trevor edits the wiki, old cases stay valid as they are; relabelling against the new wiki is a deliberate act, not a side effect.

## Where cases come from

1. Real postings Trevor has looked at, including poor fits. A set of only good fits teaches the judge to say yes.
2. Hard cases Trevor writes or picks because he can say why they are hard.
3. Not from "whatever today's judge gets wrong". That measures one model's blind spots.

**OPEN**: how many postings to start with. Proposal: 10 postings, roughly 150 requirement cases.

## Labelling

Trevor labels every case by hand on a review page before the judge's answers are shown to him, so the judge cannot anchor him. A sample is labelled twice, days apart; where he disagrees with himself, the rule in [[verdicts]] is unclear and gets fixed first.

## Metrics

| Metric | How | Why |
|---|---|---|
| **Verdict agreement** (headline) | Exact match with the label | The thing we want |
| **False yes rate** | Judge says yes, label is no or unclear | The costly error: claiming experience that is not there |
| **Quote validity** | Every quote appears word for word in the wiki at `wiki_commit` | Catches invented evidence, with no model needed |
| Unclear rate | Share of unclear verdicts | Too high and the judge is hiding; too low and it is guessing |
| Tokens, wall-clock | From the runner | Tracked, not optimised |

**OPEN**: whether agreement should weight the errors (a false yes costing more than a false unclear) or stay a plain match with false yes as a guardrail. Proposal: plain match, guardrail.

Rationales are not graded at first. If they need grading later, that is an LLM grader with a rubric of checkable claims, on a model other than the judge's.

## Splits

By posting. Train is readable by the hillclimber, validation picks between versions, test is scored each round and is the headline. No requirement from a test posting is ever read during hillclimbing.

## Noise

Before the first hillclimb round: run the baseline several times and measure how far the score moves on its own. A change smaller than that is not a result. **OPEN**: repeats per case, once the noise is known.

## What the hillclimb may change

| May change | May not change |
|---|---|
| The judge's system prompt | Cases and labels |
| How the wiki is presented to the judge | The wiki |
| Model and effort | The grader |
| | The roll-up thresholds (Trevor's call, see [[verdicts]]) |

The extraction step gets its own small eval later. Until then it is frozen out of the loop.

## After saturation

The judge is expected to pass most cases at baseline (see [[demystifying-evals]]). The eval is still worth having. What it measures from then on:

| Measure | Purpose |
|---|---|
| Agreement on the full set, as a regression suite | Shows that a wiki edit, a prompt change or a new model broke nothing |
| Same verdict on repeat runs | Trevor reads one run, so it has to be the stable one |
| Tokens and time per posting, at held agreement | The cost hillclimb's objective |
| Unclear rate across postings over time | Tracks the wiki filling in. Improved by Trevor's answers, never by the loop |

## Results on disk

The layout the [[claude-api]] skill expects, so its hillclimb loop and report builder work unchanged: `.claude/hillclimb/judge/baseline/` then `v1/`, `v2/`…, each with `results.jsonl`, `traces/`, and an `errors.jsonl` sidecar. Field reference: [`SCHEMA.md`](../raw/claude-api/shared/evals/report/SCHEMA.md) in the skill.
