# Agents SDK

> Skeleton. A constraint page. Every model call runs through the Claude Agent SDK on Trevor's Claude plan. This page says what that forces on the judge and on the eval runner, and how spend is seen.

Sources: [[Agent SDK overview]] and [[Use the Claude Agent SDK with your Claude plan]], both in `raw/`.

## Why not the Messages API

Billing. Signed in with the Claude plan, the Agent SDK draws from subscription limits ([[Use the Claude Agent SDK with your Claude plan]]). The eval tooling in [[claude-api]] assumes the Messages API and does not write Agent SDK code, so the runner here is custom. It keeps the skill's results layout so the rest of the tooling still works.

## Auth

- `claude-agent-sdk` is already a dependency (`pyproject.toml`).
- `ANTHROPIC_API_KEY` must stay **unset** in the runner's environment. Per [[Use the Claude Agent SDK with your Claude plan]], running with a Console API key draws on API credits (Max, Ultra and Team plans include a monthly amount), not on the subscription limits. The runner should check this at start and refuse to run.

## One judge call

Proposal for the first version, chosen to be as repeatable as possible:

| Setting | Value | Reason |
|---|---|---|
| Tools | none (`tools=[]`) | The judge reads; it has nothing to do |
| Wiki | Pasted whole into the prompt | A lean wiki fits, and it removes retrieval as a source of variance |
| Turns | 1 | One requirement in, one judgment out |
| System prompt | Ours, as a plain string | Not the Claude Code preset |
| Settings sources | none | Keeps Trevor's own `CLAUDE.md`, skills and memory out of eval runs |
| Output | Structured, per [[verdicts]] | Parsed in code, no scraping |
| Model, effort | Set explicitly, recorded on every row | **OPEN**: starting model |

If the wiki outgrows the prompt, the judge gets read-only `Read` and `Grep` over `wiki/` and nothing else. That is a hillclimb variant to be measured, not a default.

**OPEN**: one call per requirement (clean, attributable, more calls) or one call per posting with all requirements (cheaper, verdicts can influence each other). Proposal: per requirement.

## Seeing spend

Verified in the installed SDK (`claude_agent_sdk/types.py`): every run ends with a `ResultMessage` carrying `usage`, `model_usage`, `total_cost_usd`, `duration_ms`, `num_turns`, `is_error` and `api_error_status`.

- The runner writes these onto each row of `results.jsonl`.
- `total_cost_usd` is what the call would have cost at API prices. Nothing is charged on the plan. Keep it as a comparable unit across versions, and label it "API-equivalent" wherever it is shown.
- The real limits are the plan's usage limits and time. Budgets for a hillclimb are stated as runs and tokens.
- Each call starts a Claude Code process, so there is fixed overhead per call. Measure it once on the pilot.

## What the eval runner must get right

From the [build-eval guide](../raw/claude-api/shared/evals/build-eval.md) in [[claude-api]]; cheap now, painful to add later.

- Write each row as its case finishes; resume skips finished `(case, rep)` pairs
- A hard wall-clock ceiling per case
- Backoff with jitter on rate limits, with the retry count recorded. On a subscription, hitting the usage limit should pause the run cleanly, not fail the remaining cases.
- Failed attempts go to `errors.jsonl` with a cause (rate limit, timeout, harness error, refusal), never into the score
- Low concurrency. **OPEN**: the number, found on the pilot run.

## Calling the real thing

The runner imports and calls the same entry point in `agent/` that a normal run uses. It does not rebuild the call from the prompt and model name. Otherwise the eval measures a copy.
