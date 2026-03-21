# Negative Prompts Library

Two-layer negative prompt system for Nano Banana Pro and Veo 3.

Negative prompts are separated into two layers to enable **targeted debugging**. When a generation fails, the categorized system immediately identifies which layer to address without searching through an undifferentiated list.

---

## Two-Layer System

```
Final negative prompt = Layer 1 (Global) + Layer 2 (Scene-Specific)
```

**Layer 1 — Global** applies to every scene without exception. Never modify, never remove items.

**Layer 2 — Scene-Specific** is added per scene based on what could go wrong in that specific scene type.

> Never remove items from the global layer. Only add to it.

---

## Layer 1 — Global Blocklist

Apply to **every** Nano Banana Pro and Veo 3 prompt without exception.

```
low resolution, watermark, text overlay, CGI artifacts, audio instructions,
music references, sound design notes, voiceover cues, blurry, overexposed,
underexposed, duplicate subjects, distorted proportions
```

**Why audio instructions are in the global negative:**
Veo 3 has native audio capabilities. Without explicitly blocking audio cues in the negative prompt, the model may attempt to generate synchronized sound based on visual context — producing output that conflicts with the separately generated Google Lyria and Google Chirp tracks.

---

## Layer 2 — Scene-Specific

### S1 — Hook (macro texture / abstract)

```
human face, full body, background clutter, visible product logo too early,
artificial color saturation, smooth plastic texture where organic texture is expected,
depth of field collapse, motion blur on texture detail
```

**Why:** S1 is intentionally abstract — showing a face or the full product too early breaks the match-cut reveal and eliminates the curiosity gap that drives viewer attention into S2.

---

### S2 — Context (talent in environment)

```
deformed hands, extra fingers, plastic or waxy skin, stock-photo expression,
forced smile, wrong number of legs, mismatched shoe pairs, floating limbs,
incorrect product placement on body, over-lit flat background, visible green screen edge
```

**Why:** Talent scenes are where AI models fail most visibly — hands and skin texture are the highest-frequency failure modes. The stock-photo expression negative is critical for brand authenticity.

---

### S3 — Hero Product Shot

```
hallucinated text printed on product, wrong brand color, distorted product shape,
duplicate product instances, incorrect product proportions,
studio background with visible seams, product floating unrealistically,
shadow direction inconsistency, lens flare obscuring product detail
```

**Why:** S3 is the conversion scene — the viewer must be able to trust and identify the product. Hallucinated text on packaging is a near-universal failure mode for product shots and must be explicitly blocked.

---

### S4 — Lifestyle Montage

```
jarring cut continuity error, mismatched lighting between shots,
background crowd in sharp focus competing with talent,
product absent from frame, talent facing away with product invisible,
over-saturated skin tones, motion smear on fast cuts
```

**Why:** For CAT-1 and CAT-5, the product must be physically present in every S4 shot. "Product absent from frame" is the most common S4 failure — the model defaults to talent-only lifestyle imagery.

---

### S5 — Emotional Resolve

```
blank or glazed expression, unfocused eyes, teeth-only smile,
harsh shadows on face, unflattering angle above 45°,
product completely out of frame, cluttered background competing with face,
depth of field too shallow losing eyes to blur
```

**Why:** S5 is the last human moment before the end card — expression authenticity directly affects whether the viewer forms the intent to scan the QR code. Eyes must be sharp; expression must feel earned, not performed.

---

### S6 — End Card Background (Nano Banana Pro only)

```
gradient or texture behind QR code, QR code obscured by logo overlap,
text outside safe zone, low-contrast text on mid-tone background,
animated or moving QR pattern, logo distortion or incorrect aspect ratio,
font below 28pt equivalent, more than three visual zones competing simultaneously
```

**Why:** The end card is never AI-video-generated — it is assembled in NLE. However, the **background** is a Nano Banana Pro still frame. This scene-specific layer prevents backgrounds that would interfere with QR readability or violate safe zone rules before the editor even begins.

---

## Talent vs. Product scenes — quick reference

| Scene type | Key negatives to always include |
|------------|-------------------------------|
| Talent (S2, S4, S5) | `deformed hands, plastic skin, stock-photo expression` |
| Product (S1, S3) | `wrong color, distorted shape, hallucinated text on product` |
| Both in frame | Combine both sets |

---

## Debugging with the two-layer system

When a generation fails, identify the layer first:

| Failure | Layer | Action |
|---------|-------|--------|
| Low resolution, watermark, artifacts | Layer 1 | Verify global layer was included |
| Wrong product color | Layer 2 — S3 | Add specific color to negative: `incorrect [brand color] replaced with [wrong color]` |
| Plastic skin on talent | Layer 2 — S2/S4/S5 | Add verbatim issue from feedback: `plastic, airbrushed appearance` |
| Audio cues in Veo 3 output | Layer 1 | Verify `audio instructions, music references, voiceover cues` present |
| Hallucinated text on product | Layer 2 — S3 | Add: `any text, any characters, any writing on product surface` |

> When the same parameter fails across 3+ scenes → add the corrective language to **all** affected scenes, not just the flagged one.

---

## Adding scene-specific negatives during iteration

When Gemini returns `iteration_feedback` with `failed_parameters`, add each issue string **verbatim** into the scene-specific negative layer:

```json
"failed_parameters": [
  { "parameter": "skin_texture", "issue": "plastic, airbrushed appearance" }
]
```

Add to negative: `plastic, airbrushed appearance`

Do not paraphrase. The exact string from the feedback log should appear in the negative prompt — this creates a traceable audit trail between iteration feedback and prompt changes.

---

*Negative Prompts Library · v1.0 · Layer 1 is fixed across all productions. Layer 2 is scene and client specific.*
