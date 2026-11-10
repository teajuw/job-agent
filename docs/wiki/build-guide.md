# Job agent: build guide

> Status: skeleton, drafted 2026-11-10. Nothing below is built yet. Lines marked **OPEN** are Trevor's to decide.

This page is the whole plan on one screen. If you are a new session picking this up, read it top to bottom, then the page linked from the step you are on.

## What we are building, and why

An agent that helps Trevor apply for jobs, built from a personal knowledge wiki about him. It has two purposes: to be a tool he uses, and to be a public example of eval-driven development and hillclimbing.

It is built one feature at a time, in three phases:

1. **Requisites check** (the fit judge). Reads a posting and the wiki and says whether he is a good fit. Useful by itself, and expected to pass its eval quickly, so it becomes a regression suite and not a hill.
2. **Resume generator.** Builds a resume for one posting using only what the wiki supports. This is the hill: open-ended, with room to improve, and graded partly by the requisites check.
3. **Cover letter generator.** Same grader, plus Trevor's voice.

Why these three and in this order is on [[hills]], along with the ideas that are waiting.

## Phase 1: the requisites check

It is eval-shaped. Every required or preferred item in the posting becomes one judgment: the evidence found in the wiki, a rationale, and a verdict of **yes**, **no** or **unclear**. Code then counts the verdicts into an overall fit. The verdicts marked unclear go to Trevor to settle, and his answers improve the wiki.

```
posting ──▶ extract requirements ──▶ judge each one against the wiki ──▶ roll up (code) ──▶ fit report
                                              │                                  │
                                        knowledge/ (read-only)            unclear / flagged
                                              ▲                                  │
                                              └── Trevor approves an edit ◀── review page
```

## Three rules that do not bend

1. **The wiki belongs to Trevor.** The agent reads it and never writes facts into it. It may propose an edit; only Trevor's approval makes it true. The reason is that the whole system is worth nothing if it invents experience he does not have.
2. **No evidence, no yes.** A yes must quote the wiki, and the quote must exist there word for word. When the wiki is silent the verdict is unclear, which is a question for Trevor, not a guess.
3. **Trevor's labels are the ground truth.** The judge is the thing under test. It is scored by agreement with verdicts Trevor gave by hand, so the grader is a plain comparison in code and no second model is needed.

## Where things live

| Path | What it is | Who writes it |
|---|---|---|
| `knowledge/` | The knowledge wiki: facts about Trevor, the source of truth. **Private**, its own git repo, ignored by this one | Trevor (the agent proposes) |
| `agent/` | The judge: extract, judge, roll up | Sessions, driven by evals |
| `evals/` | Cases, labels, the runner | Sessions, with Trevor approving cases and labels |
| `.claude/hillclimb/judge/` | Run results: `baseline/`, `v1/`, `v2/`… | The runner |
| `docs/wiki/` | This build wiki | Sessions, with Trevor |
| `docs/raw/` | Source articles, never edited | Trevor |

Paths are from the repo root. `knowledge/` is the knowledge wiki, holding facts about Trevor. `docs/wiki/` is this build wiki, holding how the system is built.

## The pages

Three sources are the core of the design. Read our page on each before the step that uses it.

- [[demystifying-evals]]: the vocabulary and the roadmap for building an eval you can trust.
- [[hillclimbing]]: what makes an eval worth climbing, and how to climb without fooling yourself.
- [[claude-api]]: the skill whose commands carry out both articles.

Two pages hold the design itself.

- [[verdicts]]: what yes, no and unclear mean, the evidence rule, and how verdicts roll up. Trevor labels against this page and the judge prompt is written from it.
- [[evals]]: what a case is, where labels come from, the split, the metrics, and what the hillclimb may change.
- [[generator-evals]]: what the resume and cover letter generators are graded on: groundedness, style, bespoke.

Two pages hold constraints the design has to live inside.

- [[agents-sdk]]: every model call runs through the Agent SDK on the subscription, and how spend is seen without an API bill.
- [[knowledge-wiki]]: Trevor owns `knowledge/`; its layout, and the review loop that turns unclear verdicts into wiki edits.

[[hills]] scores the feature ideas and holds the rules against scope creep. [[decisions]] lists what is decided and what is still open. [[index]] lists every page and [[log]] records changes.

## Build order, phase 1

Each step ends in something Trevor can look at. Do not start a step until the one before it has been seen.

| # | Step | Tool | Done when |
|---|---|---|---|
| 1 | Seed `knowledge/` with the minimum, from one resume or a short interview, and make it a private git repo | see [[knowledge-wiki]] | Trevor has read every page and it is committed |
| 2 | Collect 8 to 12 real postings, including ones he would not apply to | by hand | Postings saved under `evals/postings/` |
| 3 | Write the simplest judge that works end to end | Agent SDK, see [[agents-sdk]] | One posting gives a fit report with quoted evidence |
| 4 | Build the eval: extract requirements once, freeze them, Trevor labels each | `/claude-api build-eval` | Baseline score with a confidence interval, and Trevor agrees with the grades he spot-checks |
| 5 | Audit the eval before trusting it | the eval health checklist, see [[claude-api]] | Audit findings fixed or accepted |
| 6 | Clean the judge prompt | `/claude-api prompt-audit` | Diff reviewed |
| 7 | Decide from the baseline, see below | | A written line in [[decisions]] saying which branch was taken and the numbers behind it |
| 8 | Build the review page for unclear and flagged verdicts | see [[knowledge-wiki]] | Trevor clears a queue and one wiki edit lands |

Steps 4 to 7 follow [[demystifying-evals]] and [[hillclimbing]], using the commands described in [[claude-api]].

### Step 7: what the baseline decides

Run the baseline on a small model and a strong one.

| The baseline shows | Then |
|---|---|
| Agreement around 95%, or the two models within noise of each other | Do not hillclimb quality. Move the cases to the regression suite. Run one cost hillclimb with `/claude-api hillclimb`: hold agreement, find the cheapest model, effort and call shape. |
| Real room, larger than the noise | Hillclimb quality on the hard-call cases only. Easy cases stay in the regression suite throughout. |

The first row is the expected one. It is a good result: a judge that can be trusted, and a regression suite that phase 2 relies on.

## Build order, phase 2

The resume generator. Not started until phase 1 step 8 is done. Outline only; it gets its own design page when it starts. What it is graded on is on [[generator-evals]].

| # | Step | Done when |
|---|---|---|
| 0 | Bring past resumes into `knowledge/raw/`; derive `bullets.md`. Postings they were written for are not used as eval cases | Trevor has approved the bullet bank |
| 1 | Build fictional candidates: a wiki and postings for each, so the eval can be public | Trevor has read them and finds them believable |
| 2 | Write the grader: claims trace to wiki quotes, nothing claimed where the fit judge says no, covers what the judge says yes to, length | Runs on hand-written good and bad bullets and tells them apart |
| 3 | Calibrate the taste comparison against Trevor's own picks | Agreement reported; below the bar, taste is left out of the score |
| 4 | Baseline with the simplest drafting prompt | A score with room above it and noise below the room |
| 5 | Hillclimb | Best version on the held-out set, against baseline, written up |

If step 4 shows no room, fall back to shortlist ranking, per [[hills]].

## Phase 3

The cover letter generator. Past letters come in the same way, `voice.md` is derived from them, and the phase 2 grader is reused with a voice check added. Outlined when phase 2 is done.

## Public and private

The `job-agent` repository is public. These stay out of it: `knowledge/`, eval cases and run results built on the real wiki, and third-party articles in `docs/raw/`. Public eval cases use fictional candidates. The `.gitignore` enforces this; check `git status` before any first push.

## Where this departs from the stock workflow

The claude-api skill assumes the Messages API and an API bill. Here every model call goes through the **Claude Agent SDK**, signed in with Trevor's Claude plan, which draws on subscription limits ([[Use the Claude Agent SDK with your Claude plan]]). Two consequences, both detailed in [[agents-sdk]]:

- The skill does not write Agent SDK code. The runner is ours, and it must still produce the results layout the skill's hillclimb and report builder expect.
- There is no dollar budget to approve. The limits are the plan's usage limits and wall-clock time, so budgets are stated in tokens and runs.

Quality comes first. Cost is tracked on every run, and becomes the objective only when quality has no room left.

## Things that will go wrong if nobody watches for them

- **The wiki changes and the labels go stale.** Every case records the wiki commit it was labelled against. See [[evals]].
- **Requirements from one posting land in both train and test.** Split by posting, never by requirement.
- **The judge learns the eval instead of the task.** Never paste a failing case into the prompt. Fix the rule that caused it.
- **Extraction drift hides judge changes.** Requirements are extracted once and frozen in the case, so a score change can only come from the judge.
- **Everything passes.** Expected for the fit judge; see step 7. Do not invent difficulty to keep a hillclimb alive.
- **A new idea arrives mid-build.** It goes into the table on [[hills]], not into this page.
