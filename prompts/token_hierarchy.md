# Token Hierarchy Method — T1→T5

Prompt assembly method for Nano Banana Pro and Veo 3.

AI video and image models use attention mechanisms that weight tokens by **position**, not semantic importance. A prompt like `"beautiful cinematic blue sneaker commercial"` gives equal attention weight to all adjectives — `"beautiful"` competes with `"blue"` competes with `"commercial"`.

The Token Hierarchy Method forces a top-down attention gradient by leading with the highest-value information and deferring style tokens to lower positions.

---

## Token Order

| Token | Content | Weight | Rule |
|-------|---------|--------|------|
| **T1** | Subject + Action | ~35% | Always first — never skip or delay |
| **T2** | Environment | ~20% | Brand-agnostic, reusable across clients |
| **T3** | Cinematography | ~20% | Exact mm values only — no vague terms |
| **T4** | Lighting + Grade | ~15% | Kelvin temp + practical light sources |
| **T5** | Render + Format | ~10% | Always last |

> T1-first prompts produce **40–60% fewer subject-accuracy failures** than unordered prompts of equivalent length.

---

## Rules

**Every prompt must contain all five tags in order:**
```
[T1] ... [T2] ... [T3] ... [T4] ... [T5] ...
```
Free-text prompts without tags are invalid — regenerate.

**T3 — exact lens values only:**
```
✅  85mm lens
✅  100mm macro lens
❌  wide lens
❌  telephoto
❌  close-up lens
```

**T3 — spatial movement descriptions only:**
```
✅  camera moves from 1.5m to 0.8m distance from subject
✅  camera holds static at 0.2m distance
❌  dolly in
❌  push in
❌  pull back
❌  track left
```

**T4 — Kelvin values + practical sources:**
```
✅  natural morning window light, 5000K, soft shadows
✅  golden hour backlight, 3200K, warm rim
❌  warm lighting
❌  cinematic light
❌  moody atmosphere
```

---

## Correct vs. Wrong

```
✅ CORRECT:
[T1] A woman with natural skin texture pressing serum into her cheek with fingertips.
[T2] Minimal white bathroom, no decorative props.
[T3] 50mm lens, camera moves from 0.8m to 0.6m distance from subject.
[T4] Natural morning window light, 5000K, soft shadows.
[T5] 4K resolution, cinematic, photorealistic.

❌ WRONG:
50mm lens, beautiful woman applying skincare, warm bathroom setting, cinematic lighting, 4K.
(cinematography before subject — attention weight misaligned)
```

---

## Nano Banana Pro vs. Veo 3 — key difference

Both tools use T1→T5 order. The critical difference is **T1 and T3**:

| | Nano Banana Pro | Veo 3 |
|--|----------------|-------|
| **T1** | Frozen moment — static verbs only | Action in progress — motion verbs allowed |
| **T3** | Lens + composition (no movement) | Lens + camera movement with exact distances |

### Nano Banana Pro — frozen moment rule

Nano Banana Pro generates still images. T1 must describe a **frozen instant**, not an action in progress.

**Forbidden verb forms** (imply motion):
`falling, pouring, applying, running, walking, splashing, dripping, flowing, spreading, rubbing, massaging`

**Allowed static verbs**:
`resting, sitting, standing, holding, positioned, placed, suspended, frozen, wearing`

```
❌  "A droplet falling from a dropper"
✅  "A droplet suspended mid-air beneath a glass dropper"

❌  "A person applying serum to their cheek"
✅  "A person with fingertips resting on their cheek, clear serum visible on skin"

❌  "Steam rising from a cup"
✅  "A cup with a wisp of steam frozen above the surface"
```

> Rule of thumb: if the frame **before** and **after** look different → it is motion → rewrite.

---

## Reusability — why T2 is brand-agnostic

Only T1 changes per client. T2 through T5 are reusable templates across the entire category.

| Token | Changes per client? |
|-------|---------------------|
| T1 | ✅ Always — this is the brand-specific subject |
| T2 | Rarely — environment type stays consistent within a category |
| T3 | Rarely — lens and movement are scene-position rules, not brand rules |
| T4 | Sometimes — if brand has specific color grade requirements |
| T5 | Never — render format is always the same |

This means a Physical Product brand and a Food & Beverage brand can share identical T2–T5 templates for S3 (hero product shot) and simply swap their T1 cluster.

---

## Per-scene token guidance

| Scene | T1 Focus | T3 Guidance | T4 Guidance |
|-------|----------|-------------|-------------|
| S1 — Hook | Texture or abstract subject — no face yet | 100mm macro, static or slow close orbit | Golden hour or high-contrast natural |
| S2 — Context | Person + product in environment | 35mm handheld, slight movement | Warm backlight, 3200K |
| S3 — Hero Product | Product only — no talent | 85mm, slow push-in from 1.5m to 0.8m | Clean daylight, 5500K, precise shadow |
| S4 — Montage | Single representative action | 50mm, tracking or low angle | Dynamic afternoon light |
| S5 — Resolve | Person face — genuine expression | 85mm MCU, static | Soft natural light, 3200K |

---

*Token Hierarchy Method · v1.0 · applies to all Nano Banana Pro and Veo 3 prompts in this framework*
