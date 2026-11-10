# Log

Append-only, newest last. Each entry starts `## [date] kind | title`, where kind is one of ingest, design, restructure, lint. `grep "^## \[" log.md | tail -5` shows the last five.

## [2026-11-10] design | First skeleton

Drafted the build guide and pages for verdicts, evals, the runner, the knowledge wiki and decisions. Nothing built.

## [2026-11-10] restructure | Wiki and raw folders

Pages moved into `docs/wiki/`. Sources moved from `Clippings/` into `docs/raw/`, along with `llm-wiki.md`. `README` became [[build-guide]]; `runner` became [[agents-sdk]]. Added [[index]] and this log.

## [2026-11-10] ingest | Three core sources

Wrote [[demystifying-evals]], [[hillclimbing]] and [[claude-api]]. The first two are from the articles in `raw/`; the third is from the skill as shipped in Claude Code (version 2.1.322), which has no raw copy.

## [2026-11-10] ingest | Agent SDK plan billing

Confirmed [[Use the Claude Agent SDK with your Claude plan]] is in `raw/`. Linked it from [[agents-sdk]], [[build-guide]], [[claude-api]], [[decisions]] and [[index]], and added its API-credits point to [[agents-sdk]].

## [2026-11-10] ingest | claude-api skill source

Cloned `skills/claude-api` from anthropics/skills (commit `2c5958e`, 2026-11-06) into `raw/claude-api/`. [[claude-api]] now links to the guides there, as do [[evals]], [[agents-sdk]] and [[index]]. The copy bundled with Claude Code was slightly ahead of GitHub on this date.

## [2026-11-10] design | Saturation

Expanded [[demystifying-evals]] with capability versus regression evals, graduating, and saturation, after asking whether this task can be hillclimbed at all. Open question recorded in conversation; [[build-guide]] step 7 not yet changed.

## [2026-11-10] design | Hills

Added [[hills]]: ten feature ideas scored on usefulness and climbability, a quadrant map, and a suggested order. Prompted by the fit judge being likely to saturate.

## [2026-11-10] design | Two phases

Trevor agreed the order on [[hills]]. [[build-guide]] now has two phases, with the fit judge's hillclimb conditional on its baseline and tailored bullets outlined as phase 2. [[evals]] gained an after-saturation section. [[decisions]] updated.

## [2026-11-10] design | Three features, iterative wiki, public repo

Features are now requisites check, resume generator, cover letter generator. [[knowledge-wiki]] rewritten around iterative seeding, with a proposed synthesis layer from past resumes and letters. `git init` run in `job-agent` with a `.gitignore` keeping `wiki/` and private eval data out; nothing committed or pushed.

## [2026-11-10] design | No reference grading

Dropped the seed-or-test rule from [[knowledge-wiki]]. The generators are graded without reference documents, so all past resumes and letters can seed the wiki; only their postings are kept out of the private eval.

## [2026-11-10] restructure | Repo published, wiki seeded

`job-agent` pushed as a public GitHub repo. The knowledge wiki was seeded from two resumes as five drafts in `wiki/proposed/`, awaiting Trevor's confirmation by interview. [[knowledge-wiki]] updated to the wiki's actual folders.

## [2026-11-10] design | Generator evals

Added [[generator-evals]]: the three criteria Trevor named for the generators (groundedness, style, bespoke), with the checks already scattered across [[hills]] and [[build-guide]] gathered under them. The gate, structured claims and the swap test are proposals. [[evals]] now says it covers the fit judge only. [[index]], [[build-guide]] and [[decisions]] updated.

## [2026-11-10] design | Privacy gate

Added a privacy section to [[generator-evals]]: generated drafts must contain no term from the knowledge wiki's confidential list. Agreed with Trevor.

## [2026-11-10] restructure | knowledge/

The knowledge wiki's folder is `knowledge/`, not `wiki/`. Paths updated across the build wiki and `.gitignore`.

## [2026-11-10] restructure | canon

Confirmed knowledge pages live in `knowledge/canon/`, drafts in `knowledge/proposed/`. [[knowledge-wiki]] updated.

## [2026-11-10] restructure | Knowledge wiki pushed privately

`knowledge/` now has a private GitHub remote. Internal employer names stay in `knowledge/local/`, which is not tracked. [[knowledge-wiki]] and [[generator-evals]] updated.

## [2026-11-10] design | Standard wording

Trevor asked for industry-standard wording to be part of the plan. [[knowledge-wiki]] now has a naming rule for skills, and [[generator-evals]] has a terms check under Bespoke: a code check that the draft names each matched skill in the posting's wording.
