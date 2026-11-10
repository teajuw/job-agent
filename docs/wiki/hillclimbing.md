# Automating eval design and hillclimbing

> Source page. Core to the design.
> Raw: `raw/Automating eval design and hillclimbing with Claude.md` (Lance Martin, published 2026-09-28)

The article behind steps 4 to 7 of the [[build-guide]]. It says what a well-designed eval looks like, how improving against one goes wrong, and how the `build-eval` and `hillclimb` commands in [[claude-api]] guard against that.

## A good eval has four properties

1. **Its tasks mirror real use.** Not the tasks that were easy to generate or easy to grade.
2. **Stronger models and more effort score higher.** If they do not, suspect ambiguous tasks or a miscalibrated grader.
3. **There is headroom.** The best model at the highest effort sits well below 100%, and the gap is not made of impossible tasks.
4. **Run-to-run variance is low.** High variance usually means ambiguous tasks, an unstable grader, or leftover state between trials.

## Choosing hard cases

Pick a case because a person judged it hard and can say why. Picking cases because today's model fails them measures one model's blind spots, and the next model will have different ones.

## Building the eval

In order of preference, cases come from real traffic, then bug reports, then five to ten written by hand, then synthesis. The person approves the inputs on a review page before anything is graded.

The grader is the cheapest one that fits: code when the output is constrained, a model judge with a rubric of checkable claims when it is open-ended. A handful of cases are graded and the person is asked whether they would have scored any differently.

During the baseline, three things are checked: the grader gives the same verdict twice on the same output; timeouts and errors are not being counted as failures; and the baseline is not already near 95%.

## Hillclimbing

It works best when the thing being changed is cheap to edit and revert (text, such as a prompt), when score changes can be traced to that thing, and when the objective is narrow.

**Overfitting** is the central risk: changes that raise the eval score without helping real use. Three defences:

- **Split the cases.** The hillclimber reads train. It never sees test.
- **Never paste a failure into the prompt.** Fix the rule that caused it.
- **Keep the answers out of the model's reach.**

**The loop.** Each round reads the last round's train transcripts and proposes one change. Keep it if train and test both rise. Revert it if train rises and test stays flat, or if either falls. Before the first round, check that the eval's noise is smaller than the smallest improvement worth acting on.

**When it stalls** for two or three rounds, stop editing and sort every remaining failure by cause. In the article's own example this step found that some of the remaining failures were faults in the eval: a task that asked for one thing while its grader wanted another.

**At the end**, the code is left at the version that did best on test, reported against the baseline with confidence intervals. A gain within noise is reported as no gain.

## What we take from it

| The article | Here |
|---|---|
| Tasks mirror real use | Real postings Trevor has considered, good fits and poor |
| Stronger model scores higher | A check to run once on the baseline: two models, two effort levels |
| Headroom | Hard calls from [[verdicts]] supply it: adjacent experience, year thresholds, soft skills |
| Low variance | Requirements frozen, wiki pinned, repeats measured before round one |
| Person approves inputs and grades | Trevor, on the review page |
| Cheapest grader that fits | Exact match on a three-way label |
| Split | By posting, so one posting's requirements never straddle train and test |
| Cheap, attributable surface | The judge's prompt first; model and effort second |
| Answers out of reach | Labels live in `evals/`, which the judge is never given |

## Where our case differs

- **Cost is not the objective.** The article's cost example is useful as a method, and [[agents-sdk]] explains why dollars are only a comparison unit here.
- **The wiki is a second thing that changes scores.** A better wiki raises real quality without any change to the judge. It is deliberately outside the hillclimb: the loop may not touch it, and each case is pinned to the wiki version it was labelled against. See [[evals]].
- **The stall step feeds the rubric.** When failures sort into "Trevor's label and the rule disagree", the fix belongs in [[verdicts]], not in the prompt.
