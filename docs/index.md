# Index

Every page in this wiki, one line each. Start with the [[build-guide]].

## Start here

- [[build-guide]]: the whole plan on one page, with the build order.

## Core sources

The three things the design is built from. One page per source.

- [[demystifying-evals]]: vocabulary and roadmap for an eval you can trust.
- [[hillclimbing]]: what makes an eval worth climbing, and how to climb without overfitting.
- [[claude-api]]: the skill whose commands carry out both articles.

## Design

- [[verdicts]]: the rubric. What yes, no and unclear mean, and how they roll up.
- [[evals]]: the fit judge's eval. Cases, labels, metrics, the split, and what the hillclimb may change.
- [[generator-evals]]: the resume and cover letter generators' eval. Groundedness, style, bespoke.

## Ideas

- [[hills]]: feature ideas scored by usefulness and by room to hillclimb, with a map.

## Constraints

- [[agents-sdk]]: all model calls run on the Claude plan through the Agent SDK.
- [[knowledge-wiki]]: Trevor owns the facts; the review loop that grows them.

## Running record

- [[decisions]]: decided, proposed, and open.
- [[log]]: what changed in this wiki, and when.

## Raw sources

In `docs/raw/`. Never edited.

| File | Summarised in |
|---|---|
| [[Demystifying evals for AI agents]] | [[demystifying-evals]] |
| [[Automating eval design and hillclimbing with Claude]] | [[hillclimbing]] |
| [[Agent SDK overview]] | [[agents-sdk]] |
| [[Use the Claude Agent SDK with your Claude plan]] | [[agents-sdk]] |
| [`claude-api/`](raw/claude-api/SKILL.md) (the skill, 96 files) | [[claude-api]] |
| [[llm-wiki]] | [[knowledge-wiki]] |
