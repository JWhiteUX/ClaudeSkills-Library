# Midjourney Failure Modes (v7)

Catalog of known MJ rendering failures discovered while running the bible-to-mj pipeline. Each entry: the failure pattern, why it happens, the fix, and an example.

Read this before Stage 3, especially for any scene involving binding, restraint, idols, multiple foreground figures, or supernatural elements.

---

## 1. Inline `::N` weights cause prompt errors

**Pattern:** Writing `subject::2` or `lighting::3` to emphasize a token.

**Why it fails:** The `::` separator in MJ is reserved for multi-prompt syntax (splitting one prompt into weighted segments at segment boundaries). It does not function as a per-token weight modifier. Inline use errors out.

**Fix:** Use positioning, specificity, and repetition instead.

```
WRONG: ancient furnace::3 with three figures::2 walking through flames
RIGHT: Roaring industrial-scale ancient furnace filled with blinding white-hot flame, three figures walking calmly through the inferno, unburned tunics flowing around them
```

The right version has no `::` weights but achieves the same emphasis hierarchy through opening position (furnace), reinforcement ("blinding white-hot"), and supporting detail.

---

## 2. Binding and restraints get ignored

**Pattern:** Prompts like "three bound captives" produce three captives standing free without rope.

**Why it fails:** MJ's training favors aesthetically clean compositions. Bound figures with visible rope often render as unbound figures because the model prioritizes the more "common" visual.

**Fix:** Lead with the binding as the defining subject descriptor. Add posture cues that require binding to make sense.

```
WRONG: Three bound men standing before the king
RIGHT: Three captive men with hands bound tightly behind their backs with thick rope, arms pulled back at the shoulders, wearing ornate tunics, standing forced before the king
```

**Escalation if still ignored:**
- Add "hands tied with coarse hemp rope visible at the wrists"
- Reposition to three-quarter or side angle: "side profile shot showing bound hands clearly"
- Nuclear option: add `--no untied hands, --no free arms` at the tail

---

## 3. Figurative idols render as abstract monoliths

**Pattern:** "Golden idol" or "60-cubit gold statue" renders as a featureless monolithic tower.

**Why it fails:** Without a human-form anchor, MJ defaults to abstract monumental architecture — easier to render and more visually striking.

**Fix:** Specify figurative form explicitly.

```
WRONG: A colossal 90-foot gold idol
RIGHT: A colossal 90-foot golden statue of a robed Mesopotamian king with crossed arms and crowned head
```

Add descriptive anatomy: "with crossed arms", "with raised scepter", "in seated throne pose". This forces MJ to commit to figurative rendering.

---

## 4. Small foreground subjects collapse into single figures

**Pattern:** "Three small figures standing in defiance among a vast prostrate crowd" renders as one hooded figure on a platform.

**Why it fails:** MJ favors cinematic clarity. When multiple small subjects are described as "among" a larger group, MJ often collapses them to a single hero figure to preserve compositional balance.

**Fix:** Explicit camera relationship and physical separation.

```
WRONG: Three small figures standing upright in defiance among the prostrate crowd
RIGHT: Three defiant men in robes standing shoulder to shoulder in the foreground on raised stone ground, their backs to the camera, facing the distant idol, vast crowd of thousands prostrate face-down in concentric rings beyond them
```

Key moves:
- "Foreground" — establishes camera distance
- "Shoulder to shoulder" — forces count of three to render
- "Backs to the camera" — gives MJ a definite blocking
- "Beyond them" — separates the three from the crowd spatially

---

## 5. Supernatural figures render as humans or get omitted

**Pattern:** "An angel walking with them" produces a robed man with wings, or no figure at all.

**Why it fails:** No canonical visual standard for "angel" in MJ training — defaults to either Renaissance painting tropes or omits the entity entirely.

**Fix:** Use light-based descriptions with anatomical specificity for what is and isn't visible.

```
WRONG: A divine figure walking beside them
RIGHT: A radiant humanoid silhouette of pure incandescent light walking beside them without defined facial features, glowing from within, body outlined in volumetric light rays
```

Key descriptors that work:
- "Radiant humanoid silhouette"
- "Pure incandescent light"
- "Without defined features"
- "Glowing from within"
- "Volumetric light rays"

This satisfies any constraint against depicting divine figures as people while giving MJ a concrete render target.

---

## 6. Crowd math gets wrong

**Pattern:** "Thousands of soldiers in formation" renders as 50 vaguely arranged figures.

**Why it fails:** MJ struggles with literal large counts and prioritizes compositional rhythm over headcount.

**Fix:** Specify formation pattern, not just number.

```
WRONG: Thousands of soldiers in formation
RIGHT: A vast army in concentric ring formations stretching to the horizon, ranks ten-deep in dark armor, organized into wedge-shaped divisions, scale emphasizing endless multitude
```

Pattern descriptors that work: "concentric rings", "wedge formations", "stretching to the horizon", "ranks ten-deep", "endless rows."

---

## 7. Cinematography references get clipped under word pressure

**Pattern:** Trimming a prompt to fit a word cap removes the camera/cinematographer reference, output quality drops.

**Why it matters:** Camera and cinematographer references (Arri Alexa 65, Roger Deakins, Caravaggio composition, Lubezki) anchor MJ's rendering style. Removing them sends MJ toward generic "AI cinematic" defaults.

**Fix:** When trimming, cut atmospheric padding first ("oppressive shadows pressing in" → "deep shadows"). Keep the camera reference.

---

## 8. Multiple H-tier emphasis elements compete

**Pattern:** Five different descriptors all positioned at front of prompt produces a muddled image where no element dominates.

**Why it fails:** Front-loading too many concepts forces MJ to give each one less attention.

**Fix:** Pick one C-tier opening anchor. Everything else is H, M, or L. If three elements all feel C-critical, the SCENE is doing too much — split it into two SCENEs.

---

## 9. Stylize too high washes out narrative subjects

**Pattern:** `--s 750` or higher on a narrative scene produces beautiful but abstract imagery where the actors become decorative elements.

**Fix:** Match stylize to scene type:
- Narrative beats with named actors: `--s 250-350`
- Atmospheric or visionary scenes: `--s 400-500`
- Pure mood pieces (no specific actors): `--s 500-650`
- Editorial illustration or caricature only: `--s 600-750`

---

## 10. The "biblical AI default" aesthetic

**Pattern:** Without specific direction, MJ defaults to bronze-and-gold color palettes, beam-of-light-from-heaven lighting, and Renaissance painting composition for any biblical content.

**Fix:** Override with deliberate counter-references when you want something different.

- For grit: "shot on Arri Alexa, Roger Deakins influence, muted desaturated palette"
- For brutalism: "harsh midday sun, no atmospheric haze, hard shadows"
- For Mesopotamian specificity: "basalt and lapis architectural details, dust-laden air"
- For documentary realism: "shot on 35mm film, Kodak Portra 400, naturalistic lighting"

If you want the biblical AI default, the prompt doesn't need to ask for it — it's the gravitational pull.
