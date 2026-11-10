# Generator evals

> Skeleton. What the resume and cover letter generators are graded on. The fit judge has its own page, [[evals]]. The general method lives on [[demystifying-evals]] and [[hillclimbing]].

## What is being measured

Whether a draft written for one posting is true, reads like Trevor wrote it, and was written for that posting and no other. Three criteria come first:

| Criterion | The question it answers | Fails when |
|---|---|---|
| **Groundedness** | Is every claim supported by the wiki? | The draft invents or inflates experience |
| **Style** | Does it read the way Trevor writes? | It is generic, padded, or in a voice that is not his |
| **Bespoke** | Was it written for this posting? | It could be sent to a different company unchanged |

Other measures (length, format, cost) are tracked but are not what the first hillclimb is aimed at.

## A case

One posting, one candidate's wiki at one version, one document kind.

| Field | Notes |
|---|---|
| `posting_id` | The split key |
| `candidate` | A fictional candidate for public cases, Trevor for private ones |
| `wiki_commit` | The wiki version the draft is written and graded against |
| `kind` | `resume` or `cover_letter` |
| `verdicts` | The fit judge's verdicts for this posting, frozen. Two criteria below read them |

There is no reference document. Drafts are not compared with a resume or letter Trevor once wrote for the posting (see [[knowledge-wiki]]).

## Groundedness

The criterion that must not fail. A draft that overclaims is worse than no draft.

| Check | How | Grader |
|---|---|---|
| Every claim has a quote | The generator returns each claim with the wiki quote it rests on; the quote appears word for word at `wiki_commit` | Code |
| The quote supports the claim | The claim says no more than the quote does: no added numbers, scope or seniority | Model, on a rubric of checkable claims |
| Nothing claimed against the judge | No claim addresses a requirement the fit judge marked no | Code, from `verdicts` |

The first check is the same quote validity the judge is held to in [[evals]]. The second is where inflation hides: a real quote under a claim that stretches it.

**OPEN**: whether the generator emits claims and quotes as structured output alongside the draft, or a separate step extracts claims from the finished text. Proposal: structured output, so the code check needs no model.

## Privacy

A second gate beside groundedness. The knowledge wiki holds things Trevor knows and may not publish: internal project names, and facts not cleared for release. They are listed in `knowledge/local/confidential.md`, each with the public description to use instead. That file is never pushed, so this check runs only on Trevor's machine; public cases use a made-up list for the fictional candidates.

| Check | How | Grader |
|---|---|---|
| No confidential term | The draft contains none of the listed terms | Code |
| Holds under pressure | A few cases use postings that ask about exactly the confidential thing | Code, same check |

Expected to pass every time. It is a regression check that runs on every change, not something to climb.

## Bespoke

| Check | How | Grader |
|---|---|---|
| Covers what he can offer | Each requirement the judge marked yes is addressed, the required ones first | Rubric, from `verdicts` |
| Uses the posting's terms | For each requirement the judge marked yes, the draft names the skill in the posting's own wording or a listed alias | Code, from `verdicts` and the skills page |
| Names the actual work | A cover letter refers to something specific in the posting or the company's work, not a paraphrase of its title | Rubric |
| Swap test | Shown the draft and several postings, a grader picks the one it was written for | Code scores the pick |

The terms check is the keyword match an applicant tracking system or a skimming recruiter performs. It cannot be passed by stuffing: it counts only requirements the judge marked yes, and every term must sit inside a claim that passes groundedness. A keyword with no grounded claim behind it fails the gate, not this check.

**OPEN**: the swap test is a proposal, not yet agreed. It is cheap and needs no labels, but a draft can pass it by echoing the posting's keywords, which is the stuffing that Style should catch.

## Style

| Check | How | Grader |
|---|---|---|
| Closeness to his voice | Against `voice.md`: tone, length, the phrases he uses and avoids | Rubric |
| Taste | Blind pairwise against the previous version's draft for the same case | Model, calibrated |
| Length and format | One page; bullet length; letter word count | Code |

The taste comparison is only trusted after it is calibrated against Trevor's own picks on the same pairs. Below the bar, it is reported and left out of the score ([[build-guide]], phase 2 step 3).

Style applies to both kinds, with more weight in the cover letter, where more of the text is free writing.

## How the three combine

**OPEN**. Proposal: groundedness is a gate, not a term in an average. A draft with any unsupported claim scores zero for that case, and the headline is the mean of bespoke and style over the drafts that pass. An average would let a well-written overclaim outscore an honest plain draft.

## Splits and noise

As on [[evals]]: split by posting, test scored each round, and the baseline run several times before any round so a change smaller than the noise is not read as a result. Model-graded checks add their own noise, so the grader is run on a different model from the generator and its repeat agreement is measured too.

## What the hillclimb may change

| May change | May not change |
|---|---|
| The generator's prompts and steps | Cases and frozen verdicts |
| How the wiki, `bullets.md` and `voice.md` are presented | The wiki, `bullets.md`, `voice.md` |
| Model and effort | The graders and their rubrics |
