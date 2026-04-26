---
name: kernel-angus
description: >
  Prompt engineering skill that scores prompts against the KERNEL framework
  (Keep simple, Easy to verify, Reproducible, Narrow scope, Explicit
  constraints, Logical structure) using PASS/WEAK/FAIL ratings and rewrites
  them into the TICO-V output format (Task, Input, Constraints, Output,
  Verify). Classifies the input first (raw idea, draft prompt, system prompt,
  one-liner), explains every non-PASS rating, and chains complex requests
  into sequenced sub-prompts. Use when the user invokes `/kernel-angus`,
  `/ka`, or asks to improve, refine, or structure a prompt.
---

# KERNEL-Angus: Prompt Engineering Framework

Transform vague, bloated, or underspecified prompts into tight, verifiable instructions using the KERNEL principles and TICO-V output structure.

## Activation Triggers

- `/kernel` or `/kernel-angus`
- "improve/refine/fix/tighten this prompt"
- "help me write a prompt for..."
- User pastes a prompt and asks for feedback
- User describes a goal that needs to become a prompt

## Input Classification

Identify what you're working with before analyzing. This changes your approach:

| Input Type | What It Looks Like | Your Approach |
|---|---|---|
| **Raw idea** | "I want to build a script that..." | Interview first — extract specifics before structuring |
| **Draft prompt** | A paragraph or bullet list aimed at an LLM | Score against KERNEL, then restructure into TICO-V |
| **Existing system prompt** | Multi-section instructions with role, constraints, examples | Audit section by section — flag bloat, contradictions, gaps |
| **One-liner** | "Make me a landing page" | Push back — this needs 3-5 clarifying questions minimum |

## The KERNEL Principles

Each principle is a lens for auditing a prompt. Score each as PASS, WEAK, or FAIL during analysis.

### K — Keep it simple
One clear goal per prompt. If you can't state the objective in one sentence, the prompt is doing too much.
- FAIL: Multiple unrelated goals crammed together
- WEAK: Single goal but buried under unnecessary context
- PASS: Objective is immediately obvious

### E — Easy to verify
The prompt must define what success looks like. If you can't check the output against concrete criteria, the prompt is underspecified.
- FAIL: No success criteria at all ("make it good")
- WEAK: Subjective criteria only ("make it engaging")
- PASS: Measurable or binary criteria ("return JSON with exactly 3 fields")

### R — Reproducible results
The prompt should produce consistent outputs across runs. Temporal references, ambiguous defaults, and unspecified versions break reproducibility.
- FAIL: Depends on "latest" or "current" without pinning versions
- WEAK: Some ambiguity that could drift across runs
- PASS: All references are specific and stable

### N — Narrow scope
One prompt, one job. If the prompt requires the LLM to context-switch between unrelated subtasks, split it.
- FAIL: Prompt asks for research AND code AND a summary in different domains
- WEAK: Related subtasks that could be chained but are stuffed into one prompt
- PASS: Single coherent task with clear boundaries

### E — Explicit constraints
Tell the model what NOT to do. Boundaries prevent the most common failure modes: wrong language, wrong format, unwanted libraries, hallucinated content.
- FAIL: No constraints — model freestyles everything
- WEAK: Some constraints but missing obvious ones (no length limit, no format spec)
- PASS: Positive and negative constraints that box in the output

### L — Logical structure
The prompt should flow: Context → Task → Constraints → Format. Scattered instructions force the model to reconstruct your intent.
- FAIL: Stream-of-consciousness with instructions buried mid-paragraph
- WEAK: Roughly organized but key info is out of order or repeated
- PASS: Clean separation of context, task, constraints, and expected output

## Analysis Process

Run this for every prompt you review. Do not skip the scorecard.

### Step 1: Classify the input
Identify the input type (raw idea, draft prompt, system prompt, one-liner). State it explicitly.

### Step 2: Score against KERNEL
Evaluate each of the six principles. Present as a scorecard:

```
KERNEL Scorecard
─────────────────────────────
K  Keep it simple       PASS
E  Easy to verify       FAIL  ← no success criteria defined
R  Reproducible         WEAK  ← "latest version" is ambiguous
N  Narrow scope         PASS
E  Explicit constraints FAIL  ← no format, length, or negative constraints
L  Logical structure    WEAK  ← task buried after 3 paragraphs of context
─────────────────────────────
Score: 2/6 PASS · 2/6 WEAK · 2/6 FAIL
```

### Step 3: Explain each non-PASS rating
For every WEAK or FAIL, state:
1. What's wrong (the specific violation)
2. Why it matters (the failure mode it enables)
3. How to fix it (the concrete change)

Do not explain PASS ratings unless the user asks.

### Step 4: Restructure into TICO-V
Rewrite the prompt using the TICO-V output format below. Show before/after so the user sees exactly what changed.

### Step 5: Confirm or iterate
Present the rewritten prompt and ask if it captures intent. If the user pushes back on a constraint or scope change, adjust — but explain the tradeoff.

## TICO-V Output Format

Every refined prompt lands in this structure. TICO-V stands for Task, Input, Constraints, Output, Verify.

```
Task: [Single sentence. What the model should do.]
Input: [What the model is working with — files, data, context, prior output.]
Constraints:
  - DO: [Positive constraints — required behaviors, libraries, patterns]
  - DON'T: [Negative constraints — what to avoid, exclude, skip]
Output: [Expected format, length, structure of the deliverable]
Verify: [How to confirm the output is correct — concrete checks, not vibes]
```

### TICO-V Rules
- **Task** must be one sentence. If you can't compress it, the scope is too broad — split the prompt.
- **Input** is what goes in, not what comes out. If there's no input (pure generation), say "None — generate from scratch."
- **Constraints** always has both DO and DON'T sections. If you can't think of a DON'T, you haven't thought hard enough about failure modes.
- **Output** specifies format (JSON, markdown, code file, prose), approximate length, and structure.
- **Verify** must be actionable. "It should be good" is not verification. "Run pytest and expect 0 failures" is.

## Examples

### Example 1: Raw Idea → TICO-V

**User input:** "I want a script that cleans up my CSV files"

**Scorecard:**
```
K  Keep it simple       WEAK  ← "cleans up" is vague — deduplication? formatting? validation?
E  Easy to verify       FAIL  ← no definition of "clean"
R  Reproducible         FAIL  ← no CSV structure specified
N  Narrow scope         WEAK  ← could mean 5 different operations
E  Explicit constraints FAIL  ← no language, no libraries, no error handling spec
L  Logical structure    FAIL  ← single sentence, no structure at all
Score: 0/6 PASS · 2/6 WEAK · 4/6 FAIL
```

**After KERNEL refinement:**
```
Task: Python script to deduplicate and normalize column headers in CSV files.
Input: Directory of .csv files with inconsistent headers (mixed case, trailing spaces, duplicate rows).
Constraints:
  - DO: Use pandas. Handle UTF-8 encoding. Log removed duplicates to stdout.
  - DON'T: Don't modify original files — write cleaned versions to output/. Don't drop columns.
Output: One cleaned .csv per input file in output/, plus a summary line count to stdout.
Verify: Run on test_data/ — output files should have fewer rows than input, no duplicate rows, all headers lowercase with underscores.
```

### Example 2: Bloated Prompt → TICO-V

**User input:**
> "I need you to help me create a comprehensive marketing strategy for our new product launch. First research the market, then identify our target audience, create buyer personas, develop messaging, write ad copy for three platforms, design an email sequence, and also create a social media calendar for the next quarter. Make it engaging and professional."

**Scorecard:**
```
K  Keep it simple       FAIL  ← 7+ distinct tasks in one prompt
E  Easy to verify       FAIL  ← "engaging and professional" is not measurable
R  Reproducible         WEAK  ← "new product" and "next quarter" are unanchored
N  Narrow scope         FAIL  ← this is an entire marketing department's quarterly plan
E  Explicit constraints FAIL  ← no platform specs, no tone guide, no length limits
L  Logical structure    WEAK  ← sequential but monolithic
Score: 0/6 PASS · 2/6 WEAK · 4/6 FAIL
```

**Recommendation:** Split into 4-5 chained prompts. Start with:
```
Task: Define 3 buyer personas for [product name] based on [market segment].
Input: Product spec document (attached). Competitor landscape: [list 3 competitors].
Constraints:
  - DO: Include demographics, pain points, buying triggers, preferred channels.
  - DON'T: Don't exceed 200 words per persona. Don't invent market data — use only what's provided.
Output: Markdown document with 3 persona sections, each with a summary table.
Verify: Each persona maps to a distinct segment. No overlapping demographics. All fields populated.
```

Then chain: personas → messaging framework → platform-specific copy → email sequence → calendar.

## Handling Edge Cases

**User says "just make it work":** Push back. Ask what "working" means — that's the Verify section they're skipping.

**User resists constraints:** Explain that unconstrained prompts produce inconsistent outputs. Offer to start with 2-3 light constraints and tighten after the first run.

**User pastes a 500-word prompt:** Don't rewrite blindly. Score it first, then ask which KERNEL violations they want to prioritize. Often the fix is splitting, not rewriting.

**User wants a system prompt reviewed:** Audit section by section. System prompts commonly fail on K (too many roles), N (scope creep over time), and E-constraints (accumulated contradictions). Flag redundant instructions and sections that fight each other.

**Prompt is already solid:** Say so. Score it, note the PASSes, suggest one or two minor tightening moves if any exist. Don't manufacture problems.
