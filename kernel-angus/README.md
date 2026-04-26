# Kernel Angus

An enhanced Claude skill for prompt engineering using the **KERNEL framework** — a structured approach that eliminates ambiguity and produces reproducible results. Scores prompts with a PASS/WEAK/FAIL scorecard and rewrites them into the **TICO-V** output format (Task, Input, Constraints, Output, Verify).

## The Framework

| Letter | Principle | What it means |
|--------|-----------|---------------|
| **K** | Keep it simple | One clear goal, not walls of context |
| **E** | Easy to verify | Measurable success criteria, not vague aspirations |
| **R** | Reproducible | Avoid temporal references; use exact versions and requirements |
| **N** | Narrow scope | One prompt = one goal; split complex requests |
| **E** | Explicit constraints | Say what NOT to do as well as what to do |
| **L** | Logical structure | Context → Task → Constraints → Format |

## What's Enhanced

- **Input Classification** — Every prompt is typed first (raw idea, draft prompt, system prompt, one-liner) because each type needs a different approach before scoring
- **PASS/WEAK/FAIL Scorecard** — Each KERNEL principle gets a three-tier rating with a short reason for every non-PASS, so you see exactly what to fix
- **TICO-V Output Format** — Refined prompts land in a fixed Task / Input / Constraints (DO + DON'T) / Output / Verify structure
- **Multi-Prompt Chaining** — Complex requests are split into sequenced sub-prompts with defined data flow; demonstrated inline in the bloated-prompt example rather than as a separate process
- **Edge-Case Playbook** — Built-in handling for "just make it work", users who resist constraints, 500-word prompt pastes, and system-prompt audits

## Output Format: TICO-V

TICO-V stands for **Task, Input, Constraints, Output, Verify**. Every refined prompt follows this structure:

```
Task: [Single sentence. What the model should do.]
Input: [What the model is working with — files, data, context, prior output.]
Constraints:
  - DO: [Positive constraints — required behaviors, libraries, patterns]
  - DON'T: [Negative constraints — what to avoid, exclude, skip]
Output: [Expected format, length, structure of the deliverable]
Verify: [How to confirm the output is correct — concrete checks, not vibes]
```

## Usage

Trigger the skill by:
- Typing `/kernel-angus` or `/ka` followed by your rough prompt idea
- Saying "help me with this prompt" or "refine my prompt"
- Asking for help structuring or improving a prompt

## Installation

### Claude Code (project-level)
```bash
cp -r kernel-angus/ .claude/skills/kernel-angus/
```

### Claude Code (user-level)
```bash
cp -r kernel-angus/ ~/.claude/skills/kernel-angus/
```

### Manual
Copy the `kernel-angus/` folder into your AI tool's skills directory:
- Claude Code: `~/.claude/skills/` or `.claude/skills/`

## File Structure

```
kernel-angus/
├── SKILL.md      # Skill instructions and metadata
├── README.md     # This file
└── LICENSE       # Apache 2.0
```

## Related Skills

- [context-builder](../context-builder) — Interview-based context gathering. Routes to Kernel Angus when a prompt needs restructuring.

## License

Apache 2.0

## References
- https://www.reddit.com/r/PromptEngineering/comments/1nt7x7v/after_1000_hours_of_prompt_engineering_i_found/
- https://nurxmedov.medium.com/stop-guessing-your-ai-prompts-use-this-6-step-k-e-r-n-e-l-formula-6e0953b846e0
- https://ai.plainenglish.io/after-1000-hours-of-prompt-engineering-i-found-the-6-patterns-that-actually-matters-506ca126879a
