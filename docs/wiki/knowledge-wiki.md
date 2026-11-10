# The knowledge wiki

> Skeleton. A constraint page. Rules for `wiki/` at the repo root, the source of truth about Trevor, and for the review loop that improves it. This is not the build wiki you are reading now.

## How this differs from the usual LLM wiki

`raw/llm-wiki.md` describes a wiki the model writes and the human reads. Here that is reversed for facts: **Trevor owns every claim**. A model may tidy, link, and propose, and nothing it proposes counts until he accepts it. The judge's verdicts are only as honest as this wiki.

## Built iteratively

The wiki starts at the minimum and grows through use. It is not designed up front.

1. **Seed** from one source: an existing resume, or a short interview where a session asks Trevor about his roles and he answers. Enough for the requisites check to return something.
2. **Grow** through the review loop below. Each unclear verdict is a question; each answer is a small edit.
3. **Add sources** when a feature needs them. The resume and cover letter generators are the reason to bring in past resumes and letters.

Pages appear when there is something to put in them. A page earns its place if a feature has quoted it or Trevor would miss it.

## Layers

| Layer | Path | What it holds | Rule |
|---|---|---|---|
| Raw sources | `wiki/raw/` | Resumes, cover letters, reviews, interview notes | Never edited |
| Facts | `wiki/*.md` | What is true: roles, skills, projects, preferences | Each claim traces to a raw source or to Trevor's word |
| Synthesis | `wiki/voice.md`, `wiki/bullets.md` | How Trevor presents the facts | Derived from the raw sources, approved by Trevor, each bullet traces to a fact |
| Proposals | `wiki/proposed/` | Model-drafted edits waiting for Trevor | No feature reads this folder |

The likely first fact pages are `roles`, `skills`, `projects`, `education`, `preferences`, and `answers` (questions the agent raised and Trevor's dated replies). **OPEN**: Trevor's call, made as the seed is ingested.

## Past resumes and cover letters

They are raw sources, not synthesis: documents Trevor wrote, kept as they are. They feed two things.

- **Facts.** Dates, roles and achievements go to the fact pages. Where two versions of a resume disagree, the ingest flags it for Trevor.
- **Synthesis.** Two pages the generators need and the requisites check does not:
  - `voice.md`: how he writes. Tone, length, phrases he uses and avoids, drawn from his own letters.
  - `bullets.md`: a bank of bullets he has approved, grouped by role and project, each tied to the fact it rests on. The resume generator selects and adapts from here before it writes anything new.

Two cautions.

- **A past resume is a claim, not proof.** It was written to persuade. Facts taken from one are Trevor's to confirm like any other.
- **Seed with all of them; keep their postings out of the eval.** The generators are graded without reference documents (see [[hills]]), so nothing is lost by putting every past resume and letter in the wiki. The one rule: a posting Trevor already wrote a resume for is not used as an eval case on his private set, because the generator could copy the old one.

## Writing fact pages

- Say durations and dates outright ("Python, daily, 2019 to 2025"). Year thresholds are the commonest requirement.
- Say what is absent when it matters ("No professional Go"). It lets the judge answer no where it would otherwise say unclear.
- One fact per sentence, so a quote can stand alone.

## The review loop

```
judge says unclear, or flags a verdict
        │
        ▼
review page: the requirement, the judge's evidence and rationale, the question
        │
        ▼
Trevor answers: a verdict, and optionally a fact
        │
        ├── the verdict becomes a label  ──▶ a new eval case (see [[evals]])
        └── the fact becomes a proposed wiki edit ──▶ Trevor accepts ──▶ commit
```

One answer does two jobs: it grows the eval set and it fills the wiki gap. Questions are grouped, because one missing fact usually explains several unclear verdicts across postings.

## The review page

A local page, opened in a browser. For each item it shows the posting line, the judge's quotes in context, the rationale, and three buttons plus a text box. It saves Trevor's answers to a file the next session reads.

**OPEN**: a static local HTML file that writes a JSON file, or a published artifact with stored state. Proposal: local file first, since the wiki content is personal.

The same page, with the judge's answers hidden, is used for labelling in [[evals]].

## Git

`wiki/` is its own **private** git repository. The outer `job-agent` repository is public and ignores it. Eval cases pin a wiki commit, and every accepted proposal is its own commit with the question that prompted it in the message.
