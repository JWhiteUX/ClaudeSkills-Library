# Kernel Angus

An enhanced Claude skill for prompt engineering using the **KERNEL framework** — a structured approach that eliminates ambiguity and produces reproducible results. Builds on the core KERNEL methodology with a scoring rubric, multi-prompt chaining, and extended examples across domains.

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

- **KERNEL Score** — Every prompt gets a 0–6 rubric rating before and after refinement, so you can see exactly which principles improved
- **Multi-Prompt Chaining** — Complex requests with multiple deliverables get split into sequenced KERNEL prompts with defined data flow between them
- **Extended Examples** — Before/after transformations across Code, Content, Analysis, and a full chained multi-prompt example

## Output Format

Every refined prompt follows this structure:

```
Task: [Single, clear objective]
Input: [Context and source materials]
Constraints: [What to do AND what NOT to do]
Output: [Expected format and deliverable]
Verify: [How to confirm success]
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
