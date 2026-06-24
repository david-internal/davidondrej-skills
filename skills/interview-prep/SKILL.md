---
name: interview-prep
description: Use when preparing for, running, or debriefing a candidate interview. Parses a recent candidate application CSV from a local downloads folder, builds a TLDR + flags, copies a role-specific master question list into a per-candidate file, generates custom questions, tracks asked questions + live notes during the call, compares candidates on paper, and runs a post-interview debrief.
---

# Interview Prep

This skill helps structure interview preparation and live interview note-taking while keeping reusable questions separate from candidate-specific notes.

## Files

1. **Master question files, role-specific:** `hiring/interview-questions-{role}.md`
   - One master per role, for example: `interview-questions-operations.md`, `interview-questions-content.md`, `interview-questions-engineering.md`.
   - Fallback: `hiring/interview-questions.md` if no role-specific file exists.
   - Generic, reusable questions only.
   - **Never edited during interviews.** Source of truth.
   - Numbered list 1–N.

2. **Per-candidate file:** `hiring/candidates/{candidate-slug}.md`
   - Created at the start of each interview by copying the role-appropriate master.
   - Holds: TLDR, generic questions, custom questions, candidate-returned questions if relevant, notes, asked log, post-interview verdict.
   - Everything live happens **in this file only**.
   - Treat this file as private because it may contain candidate personal data and interview impressions.

## When to Use

- "Next candidate" / "parse the CSV" → run **Parse + Init**.
- "Give me custom questions" / "attack the gaps" → run **Generate**.
- "Mark #N as asked" → run **Track**.
- "Note:" / "add to notes" / any live observation → run **Note**.
- "Compare candidates" / "CSV vs CSV" → run **Compare**.
- "Debrief" / "interview over" / "extract my impressions" → run **Post-Interview Verdict**.
- "Edit the master" → edit the master only; no candidate file involved.

## Procedure

### 1. Parse + Init candidate file

1. Confirm the role if unclear.
2. Locate the relevant local application CSV. Prefer the newest matching response/export file if multiple exist.
3. Read the schema header and candidate answer row.
4. Build a TLDR:
   - **Identity:** use only the candidate details needed for the interview context.
   - **The good:** concrete numbers, real achievements, verifiable experience.
   - **Yellow/red flags:** evasions, vague claims, contradictions, mismatches.
   - **What's missing:** dodged questions, blanks, missing portfolio/work samples.
   - **Bottom line:** one sentence verdict.
5. If the candidate references a company, channel, project, or public profile, verify only using public sources. Report concisely. If nothing can be verified, say "couldn't verify."
6. **Create the candidate file:** `hiring/candidates/{candidate-slug}.md`.
   - Read the role-specific master, or fallback master.
   - Copy its full numbered list into the candidate file under `## Generic Questions`.
   - If the applicant submitted return questions in the form, add a `## Candidate Questions to Address (end of interview)` section listing them.
   - Use the template below.

### Candidate file template

```markdown
# Interview — {Candidate Name or Initials}

**Date:** YYYY-MM-DD
**Source CSV:** {local CSV filename or reference}

## TLDR
{Identity, the good, flags, missing, bottom line}

## Generic Questions
1. {copied from master}
2. {copied from master}
...

## Custom Questions

{added in step 2 — candidate-specific only}

## Candidate Questions to Address (end of interview)
{only if the applicant returned questions in the form}

1. {their question}
2. {their question}

## Notes during interview
(none captured)

## Already Asked

- ✅ {question text}
- ✅ [candidate] {their question}
```

### 2. Generate custom questions

Generate 5–6 questions. Append them under `## Custom Questions` in the candidate file, **continuing the numbering from where the generic list ended**.

Rules:
- Direct and specific. Do not use softballs when a gap needs probing.
- Attack gaps: dodged questions, blanks, vague answers.
- Probe inconsistencies.
- Test whether stated experience holds up under specifics.
- Match the actual hiring need.
- Add one-line *italic context* under each question explaining what it probes.

If the user later says the questions are generic rather than candidate-specific:
1. Move them from `## Custom Questions` to `## Generic Questions`.
2. Keep their numbers.
3. Ask whether to promote them to the role master file too.

### 3. Track ✅ live during interview

When the user says "mark #N as asked":
1. Find #N in `## Generic Questions` or `## Custom Questions`.
2. Remove that line from the active list.
3. Append it to `## Already Asked` as `- ✅ {question text}`.
4. **Do not renumber.** Gaps are intentional.

When the user says "mark candidate #N as asked" or indicates the candidate-returned question was addressed:
- Same flow, but log as `- ✅ [candidate] {candidate question}`.

When the user says "add a new question 'X'":
- Append it to the active list with the next sequential number.

### 4. Note during interview

Append a bullet to `## Notes during interview` in the candidate file. Keep it terse: one bullet per insight.

Trigger phrases include:
- "note:"
- "add to notes"
- "observation:"
- any in-passing remark about vibe, energy, setup, professionalism, red flags, or green flags.

Always append notes. Never overwrite existing notes.

### 5. Compare candidates from CSV only

When asked to compare candidates:
1. Read the relevant source CSVs only. Do **not** use interview notes or candidate files unless explicitly requested.
2. Build a side-by-side table covering role-relevant factors, such as:
   - Availability or commitment answer.
   - Current status.
   - Role-specific proof or portfolio.
   - Scenario answer.
   - Work sample or video quality signal if relevant.
   - Curiosity, measured by number and quality of return questions.
   - Relevant eligibility or logistics requirements, if applicable.
3. End with a 1–2 sentence verdict on who is stronger **on paper**, explicitly noting that interview impressions are excluded.

### 6. Post-Interview Verdict

When the interview ends, use structured input to collect:
- Overall verdict: strong yes / maybe / pass / backup.
- Energy and vibe: high / mid / low / off.
- Availability or commitment: yes / verbal-only / no / unclear.
- Top strengths.
- Red/yellow flags.
- Next step: reject / paid trial / second round / wait / offer.
- Anything else, with a skip option.

Adapt the options to the role.

Write a `## Post-Interview Verdict` section at the bottom of the candidate file, after `## Already Asked`. Use clear headers, bullet lists for strengths and flags, and bold the verdict plus next step.

### 7. Editing a master

When the user says "edit the master":
- Confirm the role if ambiguous.
- Edit the relevant `hiring/interview-questions-{role}.md` directly.
- The master is only the numbered list — no custom, notes, or asked sections.
- **Do not touch any candidate files** when editing the master.

## Repository Hygiene

Candidate files may contain private personal data and subjective interview impressions.

- Do not push or publish candidate files unless the user explicitly confirms the destination is private and appropriate.
- Do not commit live interview notes automatically unless the workflow has been explicitly approved for that private repository.
- If the user asks to publish or share any candidate material, summarize or anonymize it first unless they explicitly request otherwise and it is lawful and appropriate.

## Pitfalls

- **Don't edit the master during a live interview.** All live actions happen in the candidate file.
- **Don't drop reusable questions during a master edit.** Many "custom" questions are role-generic. Ask before dropping.
- **Don't renumber when marking asked.** Numbers are burned. Gaps are intentional.
- **Don't restart numbering for custom questions.** Generic + custom share one continuous sequence.
- **Don't soften flags in TLDRs.** Be direct, but factual.
- **Don't fabricate verification.** If public verification finds nothing, say "couldn't verify."
- **Don't pre-fill answers.** Only mark a question asked when explicitly told.
- **Don't lose notes by overwriting.** Always append.
- **Don't mix interview impressions into a CSV-vs-CSV compare** unless explicitly requested.
- **Don't ambiguously interpret "mark #N as asked."** If both an interviewer question and candidate-returned question have the same number and both remain active, ask which one is intended.

## Verification

After each operation, verify:
- **Parse + Init:** candidate file exists with TLDR + Generic Questions + Candidate Questions if any + empty Custom/Notes/Asked sections.
- **Generate:** new `## Custom Questions` entries exist, numbering continues, italic context appears under each.
- **Track:** question is gone from active list, present as `- ✅ ...` in Already Asked, remaining numbers unchanged.
- **Note:** new bullet appears in `## Notes during interview`, nothing else changed.
- **Compare:** side-by-side table exists, no interview impressions are included, and the verdict says "on paper."
- **Post-Interview Verdict:** new section at bottom contains structured fields and bolded verdict + next step.
