# The claude-api skill

> Source page. Core to the design.
> Raw: [`raw/claude-api/`](../raw/claude-api/SKILL.md), cloned from [anthropics/skills](https://github.com/anthropics/skills/tree/main/skills/claude-api) at commit `2c5958e` (2026-11-06).
> The copy that actually runs is the one bundled with Claude Code, loaded by `/claude-api`. On 2026-11-10 the bundled copy (2.1.322) was slightly ahead of GitHub: small wording differences in four of the guides below, and a few extra files. Where they differ, the bundled one is what a session will follow.

The skill is where the two articles become commands. [[demystifying-evals]] and [[hillclimbing]] say what to do; this is what does it.

## The five tools we use

| We call it | Real name | What it is | Guide inside the skill |
|---|---|---|---|
| Build evals | `/claude-api build-eval` | A guided interview that ends in a runnable eval and a baseline | [`shared/evals/build-eval.md`](../raw/claude-api/shared/evals/build-eval.md) |
| Audit evals | no subcommand | A health checklist. `build-eval` and `hillclimb` both load it; ask for it by name to audit an existing eval | [`shared/evals/eval-audit.md`](../raw/claude-api/shared/evals/eval-audit.md) |
| Hill climb | `/claude-api hillclimb` | The improvement loop, with a split and state on disk | [`shared/evals/eval-hillclimb.md`](../raw/claude-api/shared/evals/eval-hillclimb.md) |
| Audit prompt | `/claude-api prompt-audit` | Finds dated or contradictory instructions in prompts and config files; returns a report and a proposed diff | [`shared/prompt-audit.md`](../raw/claude-api/shared/prompt-audit.md) |
| Eval cost | `/claude-api cost-optimize` | Profiles token use and proposes savings, each measured against the eval | [`shared/cost-optimization.md`](../raw/claude-api/shared/cost-optimization.md) |

## What each one does

**build-eval.** Four steps, each with a sign-off from Trevor: what is being evaluated, the inputs, the grading method, then a runnable script with its measured cost. It refuses to produce the eval until inputs, grader and cost are all approved.

**The eval health checklist.** Five groups of checks, then a report:

1. Task design: are cases unambiguous, does each have a reference answer that passes, is the set balanced and free of duplicates
2. Harness design: is the scaffolding sound
3. Metrics hygiene: are cost and latency measured correctly
4. Grader design: is the scoring right
5. Can it detect the change you are after: is the noise smaller than the improvement being sought

The fifth is the one most likely to bite here. As a rough guide from the checklist, 25 cases at 2 repeats cannot see a change smaller than about 14 points; 100 cases at 2 repeats, about 7.

**hillclimb.** Confirms a runnable eval exists, proves it can be climbed, agrees the goal and what may be changed, agrees a stopping condition, gets the plan approved, then loops: read, propose, apply, run, record. It uses a train, validation and test split, scores test every round, and keeps its state on disk so a run can resume.

**prompt-audit.** Does not stop to ask questions. It reports findings with file and line, and proposes a diff; it edits only if asked.

**cost-optimize.** Baseline first, then free wins before trade-offs. "No changes recommended" is a valid result.

## What it writes

```
.claude/hillclimb/judge/
  baseline/          results.jsonl, traces/, errors.jsonl
  v1/  v2/  …        one folder per round
  report.html        built by the skill's report script
```

- `results.jsonl`: one row per case and repeat, with the grade, tags, usage and status. The first tag is the grouping the report uses.
- `traces/`: one full transcript per case and repeat.
- `errors.jsonl`: attempts that failed before producing something scorable. They are never scored as wrong answers.
- Field reference: [`shared/evals/report/SCHEMA.md`](../raw/claude-api/shared/evals/report/SCHEMA.md).

The report is built by `shared/evals/report/build-report-lite.mjs`, run with `node` from the skill's own directory. The skill says not to hand-write a parallel report.

## Where we have to adapt it

| The skill assumes | Here |
|---|---|
| Calls go through the Messages API | Calls go through the Agent SDK, see [[agents-sdk]]. The skill says outright that it does not write Agent SDK code, so the runner is ours. |
| Each paid run needs a dollar approval | Runs draw on the Claude plan ([[Use the Claude Agent SDK with your Claude plan]]). Approvals are stated in runs and tokens. |
| Cost comes from API usage logs | Usage comes from the Agent SDK's result message. |
| A default model of its own choosing | The judge's model is named explicitly by Trevor. **OPEN** in [[decisions]]. |
| An LLM grader may be needed | Ours is code, so its "judge must differ from the model under test" rule does not arise yet. |

What must not be adapted: the results layout, the split, one change per round, and the sign-offs. Those are what make the numbers trustworthy.

## A session using it should

- Tell the skill at the start that the app runs on the Agent SDK and that the runner must call the real entry point in `agent/`.
- Point it at [[verdicts]] for the grading method and [[evals]] for the case format, so the interview confirms the design it is given.
- Let it run its sign-offs. They are Trevor's checkpoints.
