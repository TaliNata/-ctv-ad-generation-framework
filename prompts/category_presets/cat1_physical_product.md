# CAT-1: Physical Product

**Examples:** sneakers, cosmetics, electronics, clothing, home appliances.

**Core logic:** The product is the hero. Move from product intimacy → context → product as identity. The viewer discovers the product for the first time — the ad must seduce with aesthetics and aspiration.

**What makes CAT-1 different from CAT-5 (e-Commerce):**
CAT-1 assumes the viewer is encountering the product category fresh. The hook is about the product's beauty and existence. CAT-5 assumes category familiarity and focuses on purchase confidence. If the brief declares both — read the Primary Objective to determine the lead category.

---

## Scene Priorities

### S1 — Hook (0:00–0:05)
**Priority:** Macro of product material or texture → match-cut to product.

**Hint:** Preferred hook type is texture macro. Fallback: emotion hook (person discovering the product). Avoid problem hook — this category is aspirational, not pain-based.

**Why texture hook:** The abstract macro creates a curiosity gap. The viewer doesn't know what they're looking at. The match-cut reveal at 0:04 delivers the answer and locks attention into S2.

---

### S2 — Context (0:05–0:12)
**Priority:** Product in real-world context. Person using or wearing the product.

**Hint:** Show the environment the product belongs to. Golden hour preferred. The environment should feel aspirational but achievable — not fantasy, not mundane.

---

### S3 — Hero Product Shot (0:12–0:20)
**Priority:** Hero shot of product. Showcase the key feature or differentiator.

**Hint:**
- Strong design → studio void background, let the product speak alone.
- Functional product → show it in use, feature visible and legible.
- Always include key feature as on-screen super (added by editor in NLE — never in Veo 3 prompt).

**ENFORCE:** Product must appear physically in both `nano_banana_prompt` and `veo3_prompt`.

**Duration:** S3 requires a minimum 6-second hold at 6-foot viewing distance — this is a physical legibility constraint, not a stylistic choice.

---

### S4 — Lifestyle Montage (0:20–0:26)
**Priority:** Person living with the product — 3 fast cuts.

**Hint:** 1.5s cuts synced to music beats. Person + product in every shot. Music at 100%.

**ENFORCE:** The product (bottle, packaging, applicator, or worn item) must appear physically in every S4 shot. A person alone without the product is invalid for CAT-1 S4.

**OVERRIDE:** If the brief's Pacing or Tone section explicitly requests slow pacing or forbids fast cuts → replace 3-cut montage with a single held shot (4–6s) of the person with the product. ENFORCE rule still applies.

---

### S5 — Resolve (0:26–0:28)
**Priority:** Person looks to camera with genuine satisfaction. Product visible.

**Hint:** Reject stock-photo expression. Must feel real and earned. MCU 85mm.

**ENFORCE:** S5 must include **both** a person's face **and** the product in both prompts. The person must be identifiable (face visible). The product must be physically present — held, placed nearby, or visible in scene.

---

## Default Parameters

| Parameter | CAT-1 Default |
|-----------|---------------|
| `HOOK_TYPE` | Texture macro → match-cut |
| `NATURE_ELEMENT` | If applicable — organic texture tied to product material |
| `ENVIRONMENT` | Urban or lifestyle setting, golden hour |
| `TALENT_DESCRIPTOR` | Active, aspirational, 22–35, diverse |
| `TONE_KEYWORDS` | aspirational, confident, energetic, premium |
| `MUSIC_DIRECTION` | 90–115 BPM, modern electronic-acoustic |
| `VOICEOVER_STYLE` | Warm, confident, peer-to-peer — never announcer |
| `PACING` | Fast-cut hook, deliberate S3 hold (8s), beat-sync S4 |

---

## Emotional Arc

```
Curiosity (S1) → Desire (S2) → Proof (S3) → Permission (S4) → Identity (S5)
```

---

## End Card Priority

- Zone A (38%): Logo
- Zone B (30%): CTA + promo code
- Zone C (32%): QR code (optional for pure awareness campaigns)

---

*CAT-1: Physical Product · part of the CTV Ad Generation Framework category preset system*
