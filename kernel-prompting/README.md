# KERNEL Prompting Skill

A Claude skill that helps you craft sharper, more effective prompts using the **KERNEL framework** — a structured approach to prompt engineering that eliminates ambiguity and produces reproducible results.

## The Framework

| Letter | Principle | What it means |
|--------|-----------|---------------|
| **K** | Keep it simple | One clear goal, not walls of context |
| **E** | Easy to verify | Measurable success criteria, not vague aspirations |
| **R** | Reproducible | Avoid temporal references; use exact versions and requirements |
| **N** | Narrow scope | One prompt = one goal; split complex requests |
| **E** | Explicit constraints | Say what NOT to do as well as what to do |
| **L** | Logical structure | Context → Task → Constraints → Format |

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
- Typing `/kernel` followed by your rough prompt idea
- Saying "help me with this prompt" or "refine my prompt"
- Asking for help structuring or improving a prompt

## Installation

### Claude Code (project-level)
```bash
git clone https://github.com/jwhiteux/kernel-prompting-skill.git
cp -r kernel-prompting-skill/kernel-prompting .claude/skills/
```

### Claude Code (user-level)
```bash
git clone https://github.com/jwhiteux/kernel-prompting-skill.git
cp -r kernel-prompting-skill/kernel-prompting ~/.claude/skills/
```

### Manual
Copy the `kernel-prompting/` folder into your AI tool's skills directory:
- Claude Code: `~/.claude/skills/` or `.claude/skills/`
- Codex CLI: `~/.codex/skills/`

## License

Apache 2.0

## References
- https://www.reddit.com/r/PromptEngineering/comments/1nt7x7v/after_1000_hours_of_prompt_engineering_i_found/
- https://www.linkedin.com/posts/michael-opoto-41129522a_after-spending-forever-playing-around-with-activity-7380912348435345408-ISX-/
- https://nurxmedov.medium.com/stop-guessing-your-ai-prompts-use-this-6-step-k-e-r-n-e-l-formula-6e0953b846e0
- https://ai.plainenglish.io/after-1000-hours-of-prompt-engineering-i-found-the-6-patterns-that-actually-matters-506ca126879a