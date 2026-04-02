# Context Builder

A Claude skill that interviews the user before executing their prompt. Gathers missing context through focused questioning so Claude doesn't waste cycles building the wrong thing.

## Problem

Most bad outputs trace back to missing context, not bad models. Users write a prompt, Claude runs with it, and the result misses the mark because assumptions filled the gaps. A 2-minute interview saves 20 minutes of rework.

## How It Works

1. **Prompt intake** — Claude reads the full prompt, categorizes the request type (Build, Create, Analyze, Transform, Advise) and extracts what's already clear from the prompt, conversation history and memory.
2. **Gap analysis** — Evaluates the prompt against 8 context dimensions: goal, audience, scope, constraints, inputs, output format, dependencies and success criteria. Only flags what's genuinely missing and would change the output.
3. **Interview** — 2-4 questions per round, 3 rounds max. Specific questions with concrete options, not generic "what's your target audience?" filler.
4. **Routing decision** — If the interview reveals the original prompt needs restructuring (scope shifted, multiple goals, fundamentally different from what was asked), routes through the [kernel-angus](../kernel-angus) skill for KERNEL framework refinement before executing. Otherwise, executes directly.
5. **Execute** — Summarizes gathered context, states assumptions and proceeds with the original request.

## Usage

Invoke with `/context-builder` or `/cb` alongside your prompt:

```
/cb Build me a monitoring dashboard for my Proxmox cluster
```

Also responds to:
- "gather context first"
- "ask me questions before you start"
- "interview me"

## Escape Valve

Say "just do it" at any point. Claude summarizes what it knows, states assumptions and executes immediately. The skill shouldn't become a blocker.

## Kernel-Prompting Handoff

When the interview reveals the prompt itself is structurally wrong — scope shifted during Q&A, user wants multiple things, or the request is complex enough to benefit from a formal spec — Context Builder passes all gathered context to the `kernel-angus` skill. The user confirms the refined prompt before execution. No context is lost in the handoff.

## Installation

### Claude.ai
Upload the `context-builder.skill` file or the `context-builder/` folder via **Settings > Capabilities > Skills**.

### Claude Code
```bash
cp -r context-builder/ ~/.claude/skills/context-builder/
```

Or place in `.claude/skills/` at your project root for team-wide access.

## File Structure

```
context-builder/
├── SKILL.md      # Skill instructions and metadata
├── README.md     # This file
└── LICENSE        # MIT
```

## Related Skills

- [kernel-angus](../kernel-angus) — Enhanced KERNEL framework for structured prompt refinement with scoring rubric and multi-prompt chaining. Context Builder routes here when a prompt needs restructuring.

## License

MIT
