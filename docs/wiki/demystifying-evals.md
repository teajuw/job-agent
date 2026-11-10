# Demystifying evals for AI agents

> Source page. Core to the design.
> Raw: `raw/Demystifying evals for AI agents.md` (Anthropic, published 2026-01-08)

The article that gives us the vocabulary and the order of work. Its advice is to define success early, measure it plainly, and read the transcripts.

## The words it uses

| Term | Meaning | Here |
|---|---|---|
| Task | One test with inputs and success criteria | One requirement from one posting, with Trevor's label |
| Trial | One attempt at a task | One judge call on that requirement |
| Grader | Logic that scores the output | Code that compares the verdict with the label |
| Transcript | The full record of a trial | The judge's evidence, rationale and verdict, plus usage |
| Agent harness | What lets the model act | The judge in `agent/` |
| Eval harness | What runs tasks, grades and aggregates | The runner in `evals/` |

## What it says

**Three kinds of grader.** Code graders are fast, cheap and repeatable but brittle. Model graders handle open-ended output but vary between runs and need calibrating against people. Human graders are the gold standard and slow. Use code where possible, a model where necessary, and people to calibrate.

**A good task.** Two domain experts would reach the same verdict on their own. Everything the grader checks is stated in the task. A task that fails on every trial is more often broken than hard.

**Balance.** Test where a behaviour should happen and where it should not. One-sided evals produce one-sided agents.

**Grade the result, not the path.** Checking for a fixed sequence of steps punishes valid approaches.

**Give the model a way out.** A judge that may answer "unknown" invents less.

**Variance.** Outputs differ between runs, so run several trials. pass@k asks whether any of k attempts succeeds; pass^k asks whether all do. For something a person relies on each time, consistency is the one that matters.

**Read the transcripts.** A score means nothing until someone has read graded examples and found the failures fair.

## Capability, regression, and saturation

The part of the article that decides whether hillclimbing is worth doing at all.

### Two kinds of eval

| | Capability eval | Regression eval |
|---|---|---|
| Also called | Quality eval; a feature eval, when it covers something not yet built | |
| Asks | What can this do well? | Does it still do what it used to? |
| Pass rate | Starts low, on purpose | Near 100% |
| A falling score means | Nothing by itself; the tasks are meant to be hard | Something broke |
| Used for | A hill to climb | A guard while climbing elsewhere |

The article's advice is to run both. While one thing is being improved against a capability eval, the regression eval shows whether the change broke something else.

### Graduating

Once the agent passes a capability task reliably, the task has stopped measuring "can we do this at all?" and starts measuring "can we still do this?". It moves to the regression suite and is run continuously to catch drift. A capability suite is therefore meant to empty over time, and has to be refilled with harder tasks to stay useful.

### Saturation

An eval is saturated when the agent passes every solvable task. What the article says follows from that:

- **No signal.** At 100% the eval can show a regression and nothing else.
- **Slow, misleading progress near the top.** Only the hardest tasks remain, so a large real improvement shows up as a small score change.
- **The remaining failures are suspect.** Near the top, what is left is often a broken task or an unfair grader, not a weak agent. Read the transcripts before believing the gap.
- **The fix is a harder eval, not more tuning.** The article's example is a team unimpressed by a new model because their one-shot tasks could not show its gains on longer work; they built a harder eval and saw the difference.

[[hillclimbing]] adds the working rule: if the baseline is already about 95%, quality has no room left, and a hillclimb should aim at cost or speed while the score holds.

### What this means here

Most of our cases will be easy: a requirement, a short wiki, a clear rule. We should expect the judge to pass most of them on the first run. So:

- **Easy cases start in the regression suite.** They were never capability tasks.
- **The capability suite is the hard calls only** (adjacent experience, year thresholds, soft requirements, absence), and each one leaves for the regression suite as soon as [[verdicts]] has a rule for it that the judge follows.
- **Saturation is the expected outcome, and a good one.** It means the judge can be trusted, not that the eval failed. What stays worth measuring after that is in [[evals]].

## The roadmap, and where we stand

| Step in the article | Here |
|---|---|
| 0. Start early; 20 to 50 tasks from real cases is enough | We start before the judge exists |
| 1. Start from what you check by hand | Trevor reading a posting and deciding line by line |
| 2. Unambiguous tasks with reference solutions | The label is the reference; [[verdicts]] removes the ambiguity |
| 3. Balanced problem sets | Postings he fits and postings he does not; yes, no and unclear all well represented |
| 4. A harness with a stable, isolated environment | Wiki pinned to a commit per case; no user settings loaded, see [[agents-sdk]] |
| 5. Thoughtful graders | Exact match in code, plus a quote check |
| 6. Check the transcripts | The review page in [[knowledge-wiki]] |
| 7. Watch for saturation | Expected early. See the section above. |
| 8. Keep the suite alive | Every review answer becomes a new case |

## What we take from it

- **Trevor is the domain expert, and there is one of him.** The "two experts agree" test becomes "Trevor agrees with himself a week later". Where he does not, the rule is unclear.
- **The judge's three verdicts are the article's "way out" built in.** Unclear is a first-class answer, not a failure.
- **Balance is our main risk.** A judge tuned only on good-fit postings will say yes. A judge punished for every wrong yes will hide in unclear. Both rates are tracked in [[evals]].
- **Consistency matters more than best-of-k.** Trevor reads one run. The same requirement judged twice should get the same verdict.

## Where our case is unusual

The article mostly discusses evals whose grader may be an LLM judge. Here the product *is* an LLM judge, and its eval is the calibration against human labels that the article recommends for any judge. So the article's grader advice applies to our judge prompt (clear rubric, one dimension at a time, a way out), and our grader stays simple code.
