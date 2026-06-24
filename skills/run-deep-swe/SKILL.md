---
name: run-deep-swe
description: Score an AI model on the DeepSWE coding-agent benchmark via the OpenRouter API. Use when the user wants an independent, reproducible coding-agent eval — "run DeepSWE", "benchmark this model on DeepSWE", "score model X on the coding benchmark", "test a model via OpenRouter on DeepSWE", or to verify vendor-reported coding scores. Covers setup, OpenRouter wiring for mini-swe-agent, single-task / subset / full 113-task runs, and leaderboard submission.
---

# Run DeepSWE via OpenRouter

DeepSWE is a 113-task Harbor-compatible coding-agent benchmark. It runs via **Pier** driving **mini-swe-agent**. Any model reachable through OpenRouter can be scored.

## Prerequisites — state-check first

```bash
which uv git docker || echo "MISSING: install uv, git, docker"
docker info >/dev/null 2>&1 || echo "MISSING: Docker daemon not running"
echo "OPENROUTER_API_KEY set? ${OPENROUTER_API_KEY:+YES}"
```

**Docker must be running** unless using a supported cloud sandbox option.

Set `OPENROUTER_API_KEY` in your shell or secret manager before running. Never paste, commit, or invent API keys.

## Setup

```bash
git clone https://github.com/datacurve-ai/deep-swe && cd deep-swe
uv tool install datacurve-pier
# or: uv tool install git+https://github.com/datacurve-ai/pier
```

Run `pier` commands from inside the cloned benchmark repository, using relative task paths.

## OpenRouter wiring

mini-swe-agent supports OpenRouter. Both routes below use `OPENROUTER_API_KEY` and an OpenRouter model slug such as `vendor/model`.

**Route A — native OpenRouter class:**
```bash
pier run -p deep-swe/tasks --agent mini-swe-agent \
  --model vendor/model --model-class openrouter
```

**Route B — LiteLLM provider prefix fallback:**
```bash
pier run -p deep-swe/tasks --agent mini-swe-agent \
  --model openrouter/vendor/model
```

Notes:
- Slug = the exact OpenRouter slug. Verify it in the public OpenRouter model catalog before running.
- Free/zero-cost models: cost tracking can error. Set `export MSWEA_COST_TRACKING=ignore_errors` if needed.
- Flag spelling can vary by version — confirm with `pier run --help` and the agent help output.

## Smoke test first: 1 task

Always validate end-to-end wiring on a single task before spending tokens on the corpus:

```bash
# list available task ids
ls deep-swe/tasks

pier run -p deep-swe/tasks/<task-id> --agent mini-swe-agent \
  --model vendor/model --model-class openrouter
```

Pass criteria: run completes, model returns actions rather than auth/format errors, and a score or trajectory is emitted.

Troubleshooting:
- 401/auth error: check `OPENROUTER_API_KEY`.
- Provider/model mapping error: verify the slug or switch between Route A and Route B.

## Subset run

```bash
pier run -p deep-swe/tasks --agent mini-swe-agent \
  --model vendor/model --model-class openrouter \
  --n-tasks 10 --sample-seed 0
```

## Full corpus

Confirm cost and runtime before running the full benchmark.

```bash
pier run -p deep-swe/tasks --agent mini-swe-agent \
  --model vendor/model --model-class openrouter
```

If using a supported cloud sandbox, add the appropriate environment flag and ensure the cloud tool is configured locally.

## Output and reporting

- Trials are written under the benchmark runner's job output directory.
- Inspect results with the available Pier commands, such as `pier view`, `pier analyze`, or `pier critique run`.
- Report the exact command used, pass/fail, score, and any blockers.
- For official leaderboard submission, use the benchmark project's current public submission instructions.

## Failure modes

| Symptom | Cause | Fix |
|---|---|---|
| HTTP 401 | bad/missing key | re-export `OPENROUTER_API_KEY` from a secure source |
| "LLM Provider NOT provided" | missing slug prefix | use Route B `openrouter/...` or Route A with `--model-class openrouter` |
| "model isn't mapped" or cost error | unknown cost for model | `export MSWEA_COST_TRACKING=ignore_errors` |
| unknown flag | version drift | check `pier run --help` |
