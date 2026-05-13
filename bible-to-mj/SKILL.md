---
name: bible-to-mj
description: "Three-stage pipeline that converts dramatic Bible chapters into Python-style pseudocode then into Midjourney v7 prompts for cinematic scene rendering. Use this skill whenever the user mentions /bible-to-mj, asks to turn Bible chapters into Midjourney prompts, asks to pseudocode biblical narratives, requests dramatic Bible chapter curation, references the bible-to-mj pipeline, or asks to render any biblical scene as visual prompts. Also trigger when user says 'next chapter' in a conversation already using this pipeline. Handles full pipeline invocation, single-stage invocation (curation only, pseudocode only, MJ only), and chapter-specific runs."
---

# Bible to Midjourney Pipeline

Transform dramatic Bible chapters into renderable Midjourney v7 prompts via an intermediate pseudocode representation that captures actors, actions, and scene beats.

## Activation Triggers

- `/bible-to-mj <chapter>` — full pipeline for one chapter
- `/bible-to-mj curate` — return ranked list of dramatic chapters
- `/bible-to-mj next` — continue pipeline with next chapter from prior list
- "Turn {chapter} into Midjourney prompts"
- "Pseudocode {chapter} for visual rendering"
- "What are the most dramatic chapters of the Bible" (curation only)
- Any prior session has run this pipeline and user requests another chapter

## Pipeline Architecture

Three chained stages. Each stage has independent verify criteria. Never combine stages — they reason differently and combining degrades all three.

```
Stage 1: Curation     → Markdown table of 15 dramatic chapters
Stage 2: Pseudocode   → .py file with SCENE blocks (per chapter)
Stage 3: MJ Prompts   → .md file with one prompt per SCENE (per chapter)
```

When the user names a specific chapter, skip Stage 1.

## Stage 1: Chapter Curation

Run only when user requests a list, or when starting fresh without a target chapter.

**Output:** Markdown table with columns: Rank, Book Chapter, Premise (<20 words), Why Visual.

**Constraints:**
- 15 entries ranked by visual spectacle, conflict intensity, scene density
- All entries from distinct books where possible
- Cover both testaments
- No theology, no interpretation, no chapters dominated by genealogy or law

**Default seed list** (use when generating from scratch — these have been validated as visually dramatic):

Genesis 6-7 (flood), Exodus 14 (Red Sea), Exodus 32 (golden calf), 1 Samuel 17 (David and Goliath), 1 Kings 18 (Elijah vs prophets of Baal), 2 Kings 2 (Elijah's chariot of fire), Daniel 3 (fiery furnace), Daniel 5 (writing on the wall), Daniel 6 (lion's den), Ezekiel 1 (the wheels), Jonah 1-2 (storm and fish), Matthew 14 (walking on water), Matthew 27 (crucifixion), Acts 2 (Pentecost), Revelation 6 (four horsemen), Revelation 9 (locust army), Revelation 19 (rider on white horse).

## Stage 2: Pseudocode Generation

Run per chapter. Output goes to `/mnt/user-data/outputs/{book}_{chapter}.py`.

### Structure Rules

- **Classes for major actors.** Each class has state attributes (`alive`, `bound`, `bowed`, etc.). Use boolean and string state, not narrative quotes shoved into assignments.
- **Methods on classes for actions that target the actor itself.** External narrative actions become module-level functions (`carry()`, `summon()`, `report()`), not methods on inanimate objects.
- **Promote climax figures or supernatural elements to their own class.** Dict literals lose visual weight — they become abstract data structures in MJ's reading. A `MysteriousFigure` class with `appearance`, `glow`, `form`, `position` attributes gives Stage 3 concrete render targets.
- **30-80 lines.** Below 30 loses dramatic beats. Above 80 becomes verbose narration disguised as code.

### SCENE Block Contract

Every chapter gets exactly **three SCENE blocks** unless the narrative genuinely splits into more discrete acts. Three is the sweet spot — fewer loses beats, more becomes storyboard.

Format:
```python
# SCENE N: location=X, lighting=Y, mood=Z
```

All three attributes required. Stage 3 reads from these directly.

### Verification

```bash
python3 -c "import ast; ast.parse(open('FILE.py').read())"
```

Must exit 0. Parse-only — execution-clean is not required. Metaphorical function calls are fine if they parse.

Confirm three SCENE blocks each have `location=`, `lighting=`, and `mood=`.

### Anti-patterns

- Don't add modern interpretation. Don't invent details not in the text.
- Don't mix prose into assignments (`rage = "full of fury, face changed against them"`). Use constants or enums instead (`rage = MAX`).
- Don't attach methods to strings or primitives (`instrument.play()` errors on parse if you're not careful). Use module functions.

## Stage 3: Midjourney Prompt Generation

Run per pseudocode file. Output goes to `/mnt/user-data/outputs/{book}_{chapter}_mj.md`.

One MJ v7 prompt per SCENE block in the pseudocode.

### Base Parameters

All prompts end with: `--ar 21:9 --style raw --v 7`

Stylize varies by scene type — see `references/mj_failure_modes.md`.

### Emphasis Without Weights

**Never use inline `::N` weights on individual tokens.** They error in MJ every time. `subject::2` breaks the prompt.

Express emphasis through:
- **Position** — high-emphasis elements in opening, low-emphasis in tail
- **Specificity** — "Patagonia vest" beats "tech clothing"
- **Repetition** — restate the concept with different wording

| Tag from pseudocode | Implementation |
|--------------------|----------------|
| L | Late position, vague language, "subtle", "in the distance" |
| M | Mid position, stated plainly |
| H | Early position, specific detail, repeat once with different wording |
| C | Opening anchor, commanding nouns, 2-3 supporting details |

### Default Strength Assignments

- Actors: H (front position, specific descriptors)
- Setting: M (stated plainly)
- Mood: H (specific atmospheric descriptors)
- Lighting: C (opening anchor or commanding mid-prompt detail)

### Required Includes

- One camera or cinematographer reference per prompt (Arri Alexa 65, Lubezki, Deakins, Caravaggio composition, etc.)
- Concrete physical cues for any state that's prone to ignore (binding, restraint, posture)
- Specific dimension or scale callouts for visual anchors ("90-foot golden colossus" beats "huge idol")

### Hard Constraints

- Never depict divine figures as humans. Use radiant silhouettes, beams of light, or "humanoid form of pure incandescent light without defined features."
- Never use `--niji`
- Never include text overlays
- Word count: ≤75 words pre-parameters per prompt

### Failure Modes Reference

Read `references/mj_failure_modes.md` for the catalog of known MJ failure patterns and their fixes. Always consult before generating prompts for scenes involving binding, figurative idols, small foreground subjects, or supernatural elements.

### Output Format

```markdown
# {Book Chapter} — Midjourney Prompts

Source: `{book}_{chapter}.py`
Base parameters: `--ar 21:9 --style raw --v 7`

---

## SCENE 1: {Location} — {Mood}

```
{full MJ prompt on one line}
```

**Rationale:** 2-line explanation of emphasis decisions.

---

## SCENE 2: ...

(repeat per SCENE)
```

### Verification

- Every prompt ends with `--ar 21:9 --style raw --v 7` plus stylize
- No `::N` inline weights anywhere (regex check: `\w::[0-9]`)
- Word count pre-parameters ≤75 per prompt
- Every prompt has at least one camera or cinematographer reference

## Reference Files

- `references/mj_failure_modes.md` — Catalog of known Midjourney failure modes and the prompt patterns that fix them. Read before Stage 3, especially for scenes with binding, idols, foreground figures, or supernatural elements.
- `references/example_daniel_3.md` — Worked example showing the full pipeline output for Daniel 3 with annotations. Read when uncertain about pseudocode structure or MJ prompt construction.

## Common Workflows

### Full pipeline for a single chapter

User says: `/bible-to-mj Exodus 14` or "Turn Exodus 14 into Midjourney prompts"

1. Skip Stage 1
2. Run Stage 2: generate pseudocode file, verify parse, confirm three SCENE blocks
3. Run Stage 3: generate MJ prompts, verify no inline weights, verify word counts and required includes
4. Present both files via `present_files`

### Continuation in a fresh session

User pastes the pipeline_handoff_context.md from a prior session and names the next chapter.

Same as above — the handoff context is the same information this skill encodes, so prefer skill rules over handoff rules if they conflict.

### Iterating on a rendered scene

User says "MJ rendered SCENE N wrong" and uploads the image.

1. Diagnose against `references/mj_failure_modes.md` first — likely a known mode
2. Revise the prompt for that SCENE only
3. Update the .md file in place, note what changed

## Working Style

Follow Justin's preferences (from memory): no sycophancy, no reframing theater, no Oxford commas, no em-dash editorializing. Lead with the answer. Disagree directly. Code as runnable files, Python preferred.
