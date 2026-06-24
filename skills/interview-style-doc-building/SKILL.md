---
name: interview-style-doc-building
description: Use when the user wants to build a structured strategic document by answering questions, such as priorities, goals docs, framework files, ranked lists, principles, or reviews. Interview one question at a time, patch the file after each answer, then re-ask. Not for day planning.
---

# Interview-Style Doc Building

A reusable mode for creating durable strategic docs. The AI does not propose content first — it asks one question, the user answers, the AI patches the file, then asks the next question. The file is the conversation's output, updated incrementally.

## When to use

- Building a new SSOT file, such as priorities, vision, principles, frameworks, or ranked lists.
- Filling out a structured doc the user explicitly wants to author themself.
- Quarterly or annual reviews where the user's own words go into the file.

**NOT for:** day planning, task triage, or anything where the AI should propose content first.

## The Loop

1. **Create the file** with a skeleton: header, sections, and "to be filled in" placeholders. Use a single file-write operation for the new file. After this, never overwrite — only patch.
2. **Ask ONE question.** Concise. Specific. Single-faceted. Open-ended where possible.
3. **Wait for the answer.** Do not ask the next question yet.
4. **Patch the file** with the user's answer in the correct section.
5. **Re-ask** — next question, or a follow-up if the answer was incomplete.
6. Repeat until the file is complete.

## Hard Rules

- **One question at a time.** Never dump multiple questions in a single message.
- **Patch, don't overwrite.** After the initial skeleton, use patch operations for every update. Never rewrite an existing doc wholesale.
- **Update the file BEFORE asking the next question.** Order: receive answer → patch file → ask next question. Not the reverse.
- **Lists from the user are UNORDERED SETS.** When the user lists items in response to "which X should we cover?" or "what are the Ys?", treat that as a set, not a ranking. Never infer rank, priority, or sequence from the order typed. If ordering is needed, ask explicitly: "Which of these is #1?"
- **Ask dynamics, not names.** When the user references a person, do not ask "who is X?" — ask about the role or dynamic.
- **No snark, no attitude, no filler.** Use concise questions and concise acknowledgments.
- **No speculative additions.** Do not invent sections, edge cases, or "anything else?" prompts unless the user asks.

## Question Design

- **Domain-discovery, not confirmation.** "What wins against everything else?" — not "Is this #1?"
- **Surface new reality.** Each question should pull out information the AI does not already have.
- **Engine-move framing where applicable.** "What's the thing that, if true, makes the rest obvious?"
- **Concrete over abstract.** "What's #2 — the domain that wins against everything except #1?" beats "Tell me about your second priority."

## File Patching Pattern

After each answer:
1. Read the relevant section if it is not already in context.
2. Patch with `old_string` = placeholder or previous entry, and `new_string` = updated content with the user's words preserved.
3. Confirm the diff. Move on.

For ranked lists, append one rank at a time:

```markdown
1. **Domain A** — user-provided goal or principle goes here.
2. **Domain B** — user-provided goal or principle goes here.
```

Each rank gets patched in as the user confirms it.

## Common Pitfalls

- **Assuming order from a set.** User lists "A, B, C, D" → AI writes "1. A, 2. B, 3. C, 4. D" → incorrect. Always confirm rank explicitly.
- **Asking too many questions at once.** Even bundling two questions violates the rule.
- **Overwriting the file** instead of patching specific sections — this can destroy prior content.
- **Adding AI-generated content** to fill out sections. Sections stay empty until the user provides the content.
- **Skipping the file update** between Q&A pairs — the doc falls out of sync.

## Pairing with Other Skills

- `day-plan` — different pattern for planning or task triage, not interview-style document building.
- `organize-tasks` — task organization workflow.
- `memory-management` — separate from this; persona and durable preferences belong in memory.
