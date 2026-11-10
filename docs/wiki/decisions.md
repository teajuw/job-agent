# Decisions

Newest first. One line each, with the reason. An entry that is reversed is struck through, not deleted.

## Decided

- **2026-11-10** The generators are graded first on three criteria: groundedness, style, bespoke. They apply to the generators only; the fit judge keeps its own metrics. See [[generator-evals]].
- **2026-11-10** Three features: requisites check, resume generator, cover letter generator, in that order. Replaces the two-phase entry below; cover letters are common enough to be a main feature. See [[hills]].
- **2026-11-10** The knowledge wiki is built iteratively from a minimal seed (one resume or an interview), not designed up front. See [[knowledge-wiki]].
- **2026-11-10** `job-agent` is a public repo; `knowledge/` is a separate private repo that it ignores. See [[build-guide]].
- **2026-11-10** Two phases: the fit judge first, then tailored bullets as the hillclimbing showcase, with shortlist ranking as the fallback. Reason: the fit judge is useful but will likely saturate; bullets have room and reuse the judge as a grader. See [[hills]].
- **2026-11-10** The fit judge's quality hillclimb is conditional on its baseline. If there is no room, its cases become a regression suite and only cost is climbed. See [[build-guide]] step 7.
- **2026-11-10** One feature in flight at a time; a feature starts with a baseline; new ideas go to [[hills]].
- ~~**2026-11-10** Quality comes first; cost is tracked but is not a goal.~~ Replaced: cost becomes the objective when quality has no room left.
- **2026-11-10** Every model call goes through the Claude Agent SDK on the Claude plan, not the Messages API. Reason: billing, per [[Use the Claude Agent SDK with your Claude plan]]. See [[agents-sdk]].
- **2026-11-10** Each requirement gets evidence, a rationale, and a verdict of yes, no or unclear. See [[verdicts]].
- **2026-11-10** The knowledge wiki is owned by Trevor. The agent does not write facts into it. See [[knowledge-wiki]].
- **2026-11-10** Eval-driven: build the eval, audit it, then hillclimb, using the claude-api skill's tooling.

## Proposed, awaiting Trevor

- The grader is a code comparison against Trevor's labels; no LLM grader at first. ([[evals]])
- Requirements are extracted once and frozen in each case. ([[evals]])
- The roll-up is code, not a model. ([[verdicts]])
- First judge: no tools, whole wiki in the prompt, one call per requirement. ([[agents-sdk]])
- Cases pin a wiki commit; the repo goes under git. ([[evals]])
- Split by posting. ([[evals]])
- Groundedness is a gate, not a term in an average. ([[generator-evals]])
- The generator returns each claim with its wiki quote as structured output. ([[generator-evals]])
- A swap test as one of the bespoke checks. ([[generator-evals]])

## Open

Collected from the pages, in the order they block work.

1. The seed: which resume, or an interview ([[knowledge-wiki]])
1. Past resumes and letters as a synthesis layer (`voice.md`, `bullets.md`), seeded with every past document: proposed, awaiting Trevor ([[knowledge-wiki]])
2. Rules for the hard calls: adjacent experience, year thresholds, soft requirements, absence ([[verdicts]])
3. Roll-up thresholds and the shape of the final answer ([[verdicts]])
4. Starting model and effort for the judge ([[agents-sdk]])
5. How many postings to start with ([[evals]])
6. Review page: local file or published artifact ([[knowledge-wiki]])
7. Phase 2: whether public scores come from the fictional candidates alone or from both sets ([[hills]])
