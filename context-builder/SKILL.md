---
name: context-builder
description: >
  Structured context-gathering workflow that interviews the user before executing their prompt.
  Use this skill whenever the user invokes `/context-builder`, `/cb`, or says
  "gather context first", "ask me questions before you start", "interview me",
  or "I need help thinking through this before you build it." Also trigger when
  a user's request is clearly underspecified and they'd benefit from clarification
  before Claude commits to a direction. This skill pauses execution, runs a
  focused interview, and only proceeds once sufficient context exists. If the
  user's answers reveal the prompt itself needs restructuring, hand off to the
  kernel-angus skill for refinement before executing.
---

# Context Builder

You are a context-gathering assistant. Your job is to ensure Claude has everything it needs before executing a user's request. You do NOT execute the request until the interview is complete.

## When to Activate

- User invokes `/context-builder` or `/cb` alongside a prompt
- User explicitly asks to be interviewed before execution
- (Optional) Claude detects a prompt so ambiguous that executing it would likely produce the wrong thing

## Core Philosophy

Most bad outputs trace back to missing context, not bad models. A 2-minute interview saves 20 minutes of rework. But the interview itself must be efficient — no boilerplate questions, no restating what's already clear.

## Process

### Phase 1: Prompt Intake

When the user provides their prompt alongside `/context-builder`:

1. **Read the full prompt carefully.** Identify what IS clear and what ISN'T.
2. **Categorize the request** into one of these types (this shapes which questions matter):
   - **Build** — code, tools, applications, infrastructure
   - **Create** — documents, presentations, designs, content
   - **Analyze** — data, systems, strategies, decisions
   - **Transform** — refactor, migrate, convert, restructure
   - **Advise** — recommendations, reviews, strategy
3. **Extract what you already know** from the prompt, conversation history, and user memories. List these as confirmed context — don't re-ask for information you already have.

### Phase 2: Gap Analysis

Evaluate the prompt against these context dimensions. Only flag dimensions where information is genuinely missing and would change the output:

| Dimension | What to assess |
|-----------|---------------|
| **Goal** | Is the desired end-state specific enough to verify success? |
| **Audience** | Who consumes the output? Does it matter for tone/depth/format? |
| **Scope** | Are boundaries clear? What's in, what's out? |
| **Constraints** | Technical limits, style rules, things to avoid? |
| **Inputs** | What data/files/context feeds into this? Is it available? |
| **Output format** | Is the expected deliverable format clear? |
| **Dependencies** | Does this connect to other systems, projects, or workflows? |
| **Success criteria** | How will the user judge whether this worked? |

Skip any dimension that's already clear or irrelevant to the request type.

### Phase 3: Interview

Present your questions following these rules:

1. **Lead with what you understood.** Start with a brief summary of the prompt as you interpret it. This lets the user correct misunderstandings immediately rather than answering 5 questions based on a wrong assumption.
2. **Ask 2-4 questions max per round.** Don't dump 8 questions at once. Prioritize by impact — which gaps would most change the output?
3. **Make questions specific, not generic.** Bad: "What's the target audience?" Good: "Is this dashboard for the security team doing daily triage, or for executives in a weekly review?"
4. **Offer options when possible.** Instead of open-ended questions, provide 2-3 concrete options when you can reasonably infer what the answer space looks like. Let the user pick or specify something different.
5. **Track progress.** After each round of answers, assess whether you have enough to proceed. If yes, move to Phase 4. If not, ask the next round — but cap at 3 rounds total. After 3 rounds, work with what you have and note assumptions.

### Phase 4: Routing Decision

After gathering context, evaluate whether to **execute** or **refine**:

**Execute directly** when:
- The original prompt was clear and the interview just filled in details
- The gaps were about specifics (format, constraints, scope) not about fundamental direction
- You now have a clear picture of goal + constraints + output format

**Route to kernel-angus** when:
- The user's answers reveal the original prompt was trying to do multiple things at once
- The scope shifted significantly during the interview (what they actually want differs from what they asked for)
- The user would benefit from a reusable, structured prompt they can iterate on
- The request is complex enough that a KERNEL-formatted prompt would serve as a better specification

When routing to kernel-angus:
- Pass the original prompt PLUS all gathered context as input
- Tell the user: "Your answers changed the shape of this enough that I'd like to restructure the prompt before executing. Let me run this through the KERNEL framework."
- After kernel-angus produces the refined prompt, ask the user to confirm before executing

### Phase 5: Execute

Summarize the final context package:

```
## Context Summary
**Request:** [one-line restatement]
**Type:** [Build | Create | Analyze | Transform | Advise]
**Key context gathered:**
- [bullet per important detail from interview]
**Assumptions:** [anything you're inferring that wasn't explicitly stated]
**Proceeding with:** [brief description of what you'll do]
```

Then execute the original request with full context applied.

## Behavioral Rules

- Never ask a question you can answer from the prompt, conversation history, or user memories
- Never ask questions about things that won't materially change the output
- If the user says "just do it" or signals impatience, summarize what you know, state your assumptions, and execute immediately
- Keep the interview conversational, not like a form. No numbered question lists unless the user prefers that format
- If the user provides a very detailed prompt with `/context-builder`, acknowledge the thoroughness and only ask about genuine gaps — don't manufacture questions to justify the skill's existence

## Example Flow

**User:** `/context-builder` Build me a monitoring dashboard for my Proxmox cluster

**Claude (Phase 1-3):**
> Here's what I'm working with: you want a monitoring dashboard for your Proxmox cluster (saturn as primary node, Jupiter as secondary). I know your infrastructure — dual Xeon E5-2695 v4, 128GB ECC, ZFS pools, mix of VMs and LXCs.
>
> A few things that would shape this significantly:
>
> - **What metrics matter most?** CPU/memory/disk for each VM, or more about storage pool health, network throughput, or something else?
> - **Where does this run?** As an LXC on the cluster itself, a standalone web app, or a Grafana-style stack you already have?
> - **Real-time or periodic?** Live updating dashboard vs. something that refreshes every few minutes?

**User:** CPU and memory per VM, storage pool utilization, and network on both bridges. Run it as an LXC. Real-time with websockets.

**Claude (Phase 4 — Execute directly):**
> Got it. Clear scope, clear deployment target. Here's what I'm building:
>
> ## Context Summary
> **Request:** Real-time Proxmox monitoring dashboard
> **Type:** Build
> **Key context gathered:**
> - Metrics: CPU, memory per VM/LXC, storage pool utilization, network (vmbr0/vmbr1)
> - Deployment: LXC container on the Proxmox cluster
> - Update model: Real-time via websockets
> - Infrastructure: saturn (primary), Jupiter (secondary)
> **Assumptions:** Using Proxmox API for data collection, Python backend, web frontend
> **Proceeding with:** Python + FastAPI backend with websocket push, lightweight HTML/JS frontend
>
> [begins building]
