# Worked Example: Daniel 3

End-to-end pipeline output for Daniel 3 (the fiery furnace). Use as the reference pattern when generating new chapters.

## Stage 2 Output: Pseudocode

```python
"""
Daniel 3 — The Fiery Furnace
Pseudocode rendering of the dramatic arc.
"""

class King:
    def __init__(self, name, kingdom):
        self.name = name
        self.kingdom = kingdom
        self.rage = 0
        self.decree = None


class HebrewExile:
    def __init__(self, name):
        self.name = name
        self.alive = True
        self.bound = False
        self.bowed = False
        self.divine_protection = False


class Furnace:
    def __init__(self):
        self.temperature = "standard"
        self.occupants = []
        self.flame_color = "orange"

    def heat(self, multiplier):
        self.temperature = f"{multiplier}x normal"
        self.flame_color = "white-hot"

    def consume(self, target):
        if getattr(target, "divine_protection", False):
            return False
        target.alive = False
        return True


class MysteriousFigure:
    def __init__(self):
        self.appearance = "like a son of the gods"
        self.named = None
        self.glow = "radiant, unburned"
        self.form = "humanoid but otherworldly"
        self.position = "walking among the three"


# SCENE 1: location=Plain of Dura, lighting=harsh midday sun, mood=imperial spectacle
nebuchadnezzar = King("Nebuchadnezzar", "Babylon")
golden_image = {"height_cubits": 60, "width_cubits": 6, "material": "gold"}
nebuchadnezzar.decree = "all peoples bow when the music plays"

assembly = ["satraps", "prefects", "governors", "counselors", "treasurers", "judges"]
musicians = ["horn", "flute", "lyre", "trigon", "harp", "bagpipe"]

for instrument in musicians:
    play(instrument)
    for person in assembly:
        person.bowed = True

shadrach = HebrewExile("Shadrach")
meshach = HebrewExile("Meshach")
abednego = HebrewExile("Abednego")
three = [shadrach, meshach, abednego]

for hebrew in three:
    hebrew.bowed = False  # they refuse

informants = ["certain Chaldeans"]
report(target=three, charge="will not serve your gods")

# SCENE 2: location=throne room, lighting=torchlit, mood=cornered defiance
nebuchadnezzar.rage += 10
summon(nebuchadnezzar, three)
offer_second_chance(nebuchadnezzar)

response = "our God is able to deliver us, even if he does not, we will not bow"

nebuchadnezzar.rage = MAX
furnace = Furnace()
furnace.heat(multiplier=7)

strong_soldiers = select(criterion="mightiest in army")
for hebrew in three:
    hebrew.bound = True
    hebrew.clothing = ["tunic", "trousers", "hat", "cloak"]

carry(strong_soldiers, three, toward=furnace)
furnace.consume(strong_soldiers)  # flames kill the soldiers at the mouth

# SCENE 3: location=furnace interior visible through opening, lighting=white-hot flame, mood=impossible witness
for hebrew in three:
    fell_into(hebrew, furnace)
    hebrew.bound = False  # ropes burn off
    hebrew.divine_protection = True

approach(nebuchadnezzar, furnace)
count = len(furnace.occupants)

assert count == 4  # one more than were thrown in
fourth = MysteriousFigure()

call_out(nebuchadnezzar, "come out, servants of the Most High God")
for hebrew in three:
    walk_out_of(hebrew, furnace)
    assert hebrew.alive
    assert hebrew.hair_singed is False
    assert hebrew.clothing_burned is False
    assert hebrew.smell_of_smoke is False

nebuchadnezzar.decree = "any people who speaks against their God shall be torn limb from limb"
for hebrew in three:
    promote(hebrew, province="Babylon")
```

### Why this works

- **Classes capture the actors.** King, HebrewExile, Furnace, MysteriousFigure each have state attributes that Stage 3 reads.
- **Climax figure promoted to its own class.** The fourth figure is the chapter's narrative center — it deserves a class, not a dict, so Stage 3 has concrete attributes to render.
- **Module-level functions for narrative actions.** `carry()`, `summon()`, `report()` keep methods off inanimate objects while still parsing.
- **Three SCENE blocks, each with location/lighting/mood.** Stage 3 maps directly.
- **State changes mark dramatic beats.** `bound = True` then `bound = False` after the ropes burn off is the visual transformation Stage 3 renders.

## Stage 3 Output: MJ Prompts

```markdown
# Daniel 3 — Midjourney Prompts

Source: `daniel_3.py`
Base parameters: `--ar 21:9 --style raw --v 7`

---

## SCENE 1: Plain of Dura — Imperial Spectacle

```
Three defiant men in robes standing shoulder to shoulder in the foreground on raised stone ground, their backs to the camera, facing a colossal 90-foot golden statue of a robed Mesopotamian king with crossed arms in the distance, vast crowd of thousands of robed officials prostrate face-down in concentric rings around the statue across a sun-bleached desert plain, ancient Mesopotamian city architecture in distant haze, harsh white midday sun casting hard shadows, cinematic wide shot, shot on Arri Alexa 65, Roger Deakins influence --ar 21:9 --style raw --v 7 --s 300
```

**Rationale:** Three figures lead the prompt as C-anchor with explicit foreground placement and "shoulder to shoulder" to force the count. Idol gets figurative descriptors (failure mode 3). Harsh midday gets concrete behavior "casting hard shadows."

---

## SCENE 2: Throne Room — Cornered Defiance

```
Three captive men with hands bound tightly behind their backs with thick rope, arms pulled back at the shoulders, wearing ornate Babylonian tunics and cloaks, standing forced before an enraged Mesopotamian king on a basalt throne, the king's face contorted with fury and reddened with rage, massive armed guards flanking with curved bronze blades, a vertical shaft of radiant golden light rising behind the throne, torchlight raking across stone walls in deep amber streaks, Caravaggio chiaroscuro, claustrophobic medium shot, shot on medium format --ar 21:9 --style raw --v 7 --s 350
```

**Rationale:** Binding leads with maximum specificity (failure mode 2). King's rage gets reinforced through repetition: "contorted with fury and reddened with rage." Torchlight gets concrete behavior "raking across stone walls."

---

## SCENE 3: Furnace Interior — Impossible Witness

```
Three unbound men in unburned tunics walking calmly through white-hot flames inside a roaring industrial-scale ancient furnace, a fourth radiant humanoid silhouette of pure incandescent light walking beside them without defined features, the Babylonian king visible at the furnace mouth in stunned recoil, blinding white-hot flame light spilling outward in volumetric rays, sparks suspended midair, awe and impossibility, cinematic wide shot, Emmanuel Lubezki influence, high dynamic range --ar 21:9 --style raw --v 7 --s 400
```

**Rationale:** Three walking figures lead. The fourth figure uses failure mode 5 fix: "pure incandescent light walking beside them without defined features." King at the mouth is the reaction shot at default weight. Stylize at 400 for flame physics.
```

## Verification record

- All three prompts: no `::N` inline weights, valid v7 parameters, under 75 words pre-parameters
- Pseudocode parses with `ast.parse`, three SCENE blocks each with location/lighting/mood
- Camera/cinematographer reference in every prompt

## Render outcomes from MJ

These outcomes inform the failure modes catalog:

- **SCENE 1:** First render collapsed three figures to one hooded silhouette and the figurative idol to an abstract monolithic tower. Documented as failure modes 3 and 4. Fix applied above.
- **SCENE 2:** First render produced unbound captives. Documented as failure mode 2. Fix applied above. Vertical beam of light behind throne emerged organically from MJ — kept as artistic interpretation since the king claims divine authority being defied.
- **SCENE 3:** Not yet rendered.
