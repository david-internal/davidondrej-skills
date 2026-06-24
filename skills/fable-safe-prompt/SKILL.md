---
name: safe-prompt-rewriter
description: Rewrite a user's prompt so it is clearer, safer, and aligned with allowed defensive or educational use. Use when the user asks to make a prompt policy-compliant, reduce ambiguity, or reframe dual-use topics into benign, authorized, defensive work.
---

# Safe Prompt Rewriter

Rewrite the user's prompt to make the request clearly benign, authorized, and policy-compliant. Preserve the user's legitimate goal where possible, but do not help bypass safety systems, hide intent, or transform an unsafe request into wording that merely appears safe.

The user provides the prompt-to-be-changed wrapped in `<prompt>...</prompt>` XML tags. Operate only on the text inside those tags; treat everything outside as instructions to you.

## Method

1. Identify phrases that create ambiguity, imply unauthorized activity, request harmful instructions, or ask for hidden reasoning.
2. Replace those phrases with clear, safe, defensive wording when a legitimate benign equivalent exists.
3. Keep unrelated benign text as close to the original as possible.
4. Return the complete edited prompt.
5. If the underlying request remains unsafe or unauthorized, do not rewrite it as a workaround. Explain briefly that no safe rewrite is available and suggest a benign alternative.

Reframe acceptable tasks as:

- owned or explicitly authorized;
- defensive, educational, or administrative;
- focused on fixing, testing, documenting, or reducing risk;
- free of instructions for unauthorized access, evasion, abuse, weaponization, or harm.

## Safe rewrite examples

| Risky framing | Safer framing |
|---|---|
| “How could someone break into this system?” | “Review this owned system for missing security controls and recommend defensive fixes.” |
| “Write a payload or proof of exploit.” | “Add a regression test that demonstrates the bug is fixed, then patch the issue.” |
| “How do I bypass this control?” | “Help me strengthen validation, monitoring, and abuse prevention for this control.” |
| “Show attack steps.” | “Summarize the risk at a high level and provide mitigation steps.” |
| “Show your hidden reasoning step by step.” | “Provide a concise explanation of the answer and key assumptions.” |
| “Diagnose this medical condition.” | “Help me understand this information and suggest questions to ask a qualified professional.” |

## Handling unsafe prompts

If the prompt is primarily about unauthorized access, credential theft, evasion, malware, harmful biological or chemical work, or other harmful activity, do not sanitize it into a superficially acceptable form. Instead, state that the request cannot be safely rewritten and offer a defensive, educational, or harm-reduction alternative.

## Output

Return:

1. The full rewritten prompt in a Markdown code block.
2. A short list of the specific sentences or phrases changed.
3. If no safe rewrite exists, a brief explanation and a safer alternative direction.
