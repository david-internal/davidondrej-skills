---
name: pre-production
description: YouTube video pre-production. Use when preparing a video that involves a tool, repo, product, build, or use-case demo. Two phases — (1) VET the tool actually works before committing recording time, then (2) BUILD the simplest reproducible steps the creator records from. Triggers on pre-production, pre-prod, test before filming, vet this tool, try it out, sandbox test, viability check, does this actually work, use case testing, figure out simplest steps, reproducible steps, demo prep, build the use case.
user-invocable: true
---

# YouTube Pre-Production

Two phases of getting a video ready to record:

1. **VET** — does the tool/repo/product actually work as advertised? Find the demo-worthy moments. Make a go/no-go decision before investing recording time.
2. **BUILD** — once it's a go, figure out the simplest reproducible path to the end outcome and write it up as a clean step-by-step file the creator records from.

Phase 1 is for tool/repo/product videos. Phase 2 is for any build/use-case video. Many videos need both; some need only one.

## Video Outline

A "Video Outline" is what the actual video will look like: the structure of the video.

The outline is a beat-by-beat skeleton, written into the video's markdown file. It is high-level beats with a few talking points under each — not a full script. The creator fills the exact intro and wording at record time; the outline locks the order and the points they must hit.

### Proven outline structure

Use this as the default scaffold; adapt per video.

1. **Hook (first ~15 seconds).** Open with the payoff, not the setup. Pattern: "[Tool] is powerful" + a stacked list of the outcomes it unlocks, such as better performance, lower costs, or a useful business workflow. If a credible public proof exists, mention it here.
2. **Problem + promise.** Name the old pain, then state the agenda plainly: "in this video I'm going to show you A, B, and C."
3. **Retention hook + objection crushing.** Explain why viewers should watch until the end. Pre-empt common excuses quickly: "isn't this complicated? No — one install. Too expensive? It's affordable. Not technical? If you can copy-paste, you can do this."
4. **Setup / install.** Step-by-step from scratch, plain English, zero assumed knowledge. Mention any public resources linked with the video if relevant.
5. **Main demo.** The walkthrough. Every step explained with its WHY. This is where the pre-production phases above pay off — the vetted demo moments and the stripped reproducible steps go here.
6. **Sponsor or resource beat, if applicable.** Weave it in natively, not bolted on. The value must be a real beat in the workflow.
7. **Soft mid-video CTA.** Slip in "subscribe" naturally without breaking flow. Avoid a hard CTA in the first minute.
8. **Outro CTA.** Recap the transformation -> push action -> relevant links or resources.

### Retention rules baked into the outline

- Open on the outcome, never the backstory.
- State what viewers get by watching to the end early, and re-tease it mid-video with a different reason.
- Crush objections before the viewer can raise them.
- Every section must add new value; no repeated examples or points.

### What makes an outline bad

- A weak or slow hook that buries the payoff.
- "A video about everything" — no single hero use-case as the spine.
- A sponsor or resource segment that feels bolted on instead of native to the workflow.
- No retention hook / no clear reason to keep watching.
- Steps with no WHY behind them.

## How to Communicate With the Creator

The creator needs to understand every step deeply enough to explain it to viewers on camera. This is non-negotiable.

**Before each step:** say what you're about to do in one sentence and WHY it's needed.

**After each step:**

- Report whether it worked as expected.
- **"What just happened:"** — explain in plain language what the step actually did under the hood. No jargon without explanation. Audience ranges from beginners to advanced.
- **"Why we need it:"** — connect the step to the overall goal. Why can't we skip it?
- **"Next step:"** — preview what's next and ask before proceeding.

**Pacing rules:**

- ONE step at a time. Never batch or rush ahead.
- Wait for go-ahead before each step.
- If something fails, explain what went wrong and why before fixing.
- Keep explanations concise but thorough — short sentences, plain language.
- NEVER assume the creator already understands a concept. Nothing is "obvious."

**The goal:** after this process, the creator can sit in front of a camera and walk viewers through every step from memory — not just WHAT to do but WHY each step matters.

## Where Testing Happens

**Test on the exact same setup that will be used for recording.** Recording on a VPS -> test on a VPS. Recording on a local machine -> test on a local machine. Never test in one environment and expect steps to work in a different one on camera.

Never install into main project directories — always use a safe sandbox directory such as `sandbox/`.

---

# Phase 1 — VET: Tool/Repo/Product Viability

### 1. Understand the tool

- Read the existing video notes if they exist.
- Identify what the tool claims to do and the hook for the video.
- Ask what specific claims/features need to be verified.

### 2. Set up in sandbox

- Clone/install into `sandbox/`, step by step, explaining each step.
- Document setup friction, because viewers may hit the same issues.

### 3. Run the four-test viability check

Escalating difficulty. Each test must pass before the next:

| Test | Purpose | What it proves |
|---|---|---|
| **Sanity** | Trivial task | The tool runs at all |
| **Stress** | Complex/form-heavy task | Handles real-world complexity |
| **Self-healing** | Task requiring adaptation | The key differentiator works |
| **Real-workflow** | The intended real use case | It's useful, not just a demo toy |

- Run one test at a time.
- After each test, explain what happened, whether it matched expectations, and any surprises.
- Diff any files the tool modified.

### 4. Document results

Summarize:

- Which tests passed/failed.
- Where the strongest demo moments are.
- Gotchas viewers will hit.
- Go/no-go recommendation.

Update the video notes with the findings.

### 5. Go/no-go decision

Present findings. The creator decides whether to commit.

---

# Phase 2 — BUILD: Simplest Reproducible Steps

Once vetted, figure out the cleanest path to the end outcome so the recording can be clean.

**Core principle: Start with the end outcome. Then find the simplest reproducible path to it.** Trial-and-error is fine during exploration — NOT in the final output. Strip everything that wasn't actually needed.

### 1. Define the end outcome

Write exactly what the use case looks like when it works.

- What is being demonstrated?
- What's the money shot?
- What does the viewer see at the end?

If unclear, ask — don't guess.

### 2. Explore freely: trial and error allowed

Figure out how to achieve the outcome. Try, fail, iterate. Messy is fine here — none of it goes in the final output yet.

### 3. Strip to the simplest reproducible path

Remove every unneeded step, dead end, abandoned attempt, and debugging detour. Keep only what a fresh user needs to reproduce the result.

### 4. Write the clean step-by-step markdown

Create one markdown file with:

- The end outcome: what we're building/showing.
- Step-by-step instructions, in order.
- For EACH step: a short WHY — what it does, what breaks without it.
- Non-obvious gotchas: things that look broken but aren't.
- Known issues: what they look like and how to fix them.

Plain English. No advanced terminology without explanation. The creator must be able to read this on camera and explain every step without guessing.

**Output location:** one file per use case inside the video's working folder, alongside the main video notes. Use generic, non-private file names such as `use-case-01.md`.

### 5. Verify the steps work multiple times

Not optional. Run the stripped-down steps from scratch on a fresh setup more than once to catch flakiness or hidden state. If anything fails, fix the file and run again. Only hand it off once it reproduces cleanly every time. The creator should hit zero surprises while recording.

---

## Pitfalls

- **Don't rush tests.** The creator needs to understand every step for the video.
- **Don't skip the self-healing test** in Phase 1 — it is often the key differentiator. If it fails, rethink the angle.
- **Don't install into main project directories** — always use `sandbox/`.
- **Don't assume marketing claims are true** — the whole point is to verify.
- **Don't include trial-and-error in the final steps** in Phase 2 — minimal reproducible path only.
- **Don't skip the WHY** on any step — no reasoning = unusable for recording.
- **Don't guess the end outcome** — ask.

## Verification

- Phase 1: four tests run and documented; video notes updated with go/no-go.
- Phase 2: clean step-by-step file reproduces the outcome cleanly, every time, on a fresh setup.
- The creator has enough understanding to explain the whole thing on camera.
