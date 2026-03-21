# CTV Ad Generation Framework

An automated pipeline for generating 30-second Connected TV video ads using Google Cloud AI — from advertiser website URL to production-ready creative.

Built as a prompt engineering framework for SMB advertisers, designed to scale across business categories without manual creative input.

---

## What This System Does

A traditional CTV ad requires a brief, a creative team, a shoot, and weeks of production. This framework replaces that with a fully automated pipeline:

```
Advertiser website URL
        ↓
AI Analysis Pipeline (crawl → classify → analyze → fuse)
        ↓
Canonical Creative Brief JSON (auto-generated)
        ↓
Gemini 3 Pro (orchestrator — reads brief, applies category preset, assembles prompts)
        ↓
┌─────────────────┬──────────────────┬───────────────────┬──────────────────┐
│ Nano Banana Pro │     Veo 3        │  Google Lyria     │  Google Chirp    │
│ (reference      │ (video generation│ (music — 6-zone   │ (voiceover —     │
│  frames)        │  per scene)      │  timecoded)       │  per-line script)│
└─────────────────┴──────────────────┴───────────────────┴──────────────────┘
        ↓
Editor assembles in NLE + static end card overlay
        ↓
30-second 4K CTV master
```

---

## Repository Structure

```
ctv-ad-generation-framework/
│
├── README.md
│
├── system-prompt/
│   └── gemini_system_prompt.md        # Full system prompt — paste into Gemini once
│
├── prompts/
│   ├── token_hierarchy.md             # T1→T5 prompt assembly method
│   ├── negative_prompts_library.md    # Global + scene-specific negative prompts
│   └── category_presets/
│       ├── cat1_physical_product.md
│       ├── cat2_food_beverage.md
│       ├── cat3_local_service.md
│       ├── cat4_professional_services.md
│       └── cat5_ecommerce.md
│
├── examples/
│   └── the_ordinary/
│       ├── creative_brief.md          # Filled brief for The Ordinary Vitamin C serum
│       └── gemini_output.json         # Real JSON output from Gemini
│
├── docs/
│   └── fine_tuning_protocol.md        # 4-stage iteration protocol + 60% rule
│
└── assets/
    └── ai_analysis_dag.png            # AI Analysis pipeline diagram
```

---

## Prompt Engineering Architecture

### Token Hierarchy Method (T1→T5)

Every prompt — for both Nano Banana Pro and Veo 3 — follows a strict token order that matches how AI video models weight attention:

| Token | Content | Weight | Rule |
|-------|---------|--------|------|
| T1 | Subject + Action | ~35% | Always first |
| T2 | Environment | ~20% | Brand-agnostic, reusable |
| T3 | Cinematography | ~20% | Exact mm values only |
| T4 | Lighting + Grade | ~15% | Kelvin temp + practical sources |
| T5 | Render + Format | ~10% | Always last |

Token order is not stylistic — it directly affects generation quality. T1-first prompts produce 40–60% fewer subject-accuracy failures than unordered prompts of equivalent length.

### Two-Layer Negative Prompt System

Negative prompts are separated into two layers to enable targeted debugging:

- **Layer 1 — Global:** applies to every scene without exception
- **Layer 2 — Scene-specific:** added per scene based on failure modes unique to that scene type

### Category Preset System

The same 5-scene AIDA-E structure works for any business — but the emotional logic, hook type, and scene actions must change per category. The preset system encodes these differences at the structural level, not just the visual level.

| Category | S1 Hook Type | Core Emotional Logic |
|----------|-------------|----------------------|
| Physical Product | Texture macro | Aspiration — seduce with aesthetics |
| Food & Beverage | Process/texture | Sensory desire |
| Local Service | Problem hook | Anxiety relief |
| Professional Services | Emotion hook | Trust and authority |
| e-Commerce (DTC) | Social proof / scarcity | Purchase confidence |

---

## Narrative Architecture: AIDA-E

Every ad follows a 5-scene compressed AIDA-E funnel:

| Scene | Timecode | Function | AIDA-E Stage |
|-------|----------|----------|--------------|
| S1 | 0:00–0:05 | Pattern interrupt | Attention |
| S2 | 0:05–0:12 | World + context | Interest |
| S3 | 0:12–0:20 | Product proof | Desire |
| S4 | 0:20–0:26 | Emotional permission | Action |
| S5 | 0:26–0:28 | Resolve | Encode |
| End Card | 0:28–0:30 | QR conversion | Encode |

The **E (Encode)** stage is a CTV-specific addition. On digital, the viewer can click at peak desire. On CTV, they cannot — the final 4 seconds must bridge the gap between desire and the physical act of picking up a phone and scanning a QR code.

---

## Fine-Tuning Protocol

Production follows a 4-stage iteration protocol designed to catch errors at the cheapest possible point:

1. **Structural Validation** — verify all parameters and timing before any generation
2. **Reference Frame Calibration** — Nano Banana Pro still images validate color/composition before video
3. **Scene-by-Scene Video Iteration** — 60% Rule determines whether to fix or regenerate
4. **Assembly Continuity Check** — color grade, talent, and pacing verified across all scenes

**The 60% Rule:** if 60%+ of a generated frame is correct — lock seed, fix one parameter. If less than 60% — regenerate from scratch with seed: null.

---

## Key Design Decisions

| # | Decision | Rationale |
|---|----------|-----------|
| D1 | AIDA-E narrative model | Maps to CTV viewer psychology — Encode phase bridges desire and QR scan |
| D2 | Gemini 3 Pro as orchestrator | 4 tools need 4 different prompt grammars — one orchestrator removes that dependency |
| D3 | Nano Banana Pro before Veo 3 | Still images validate color/composition before expensive video generation |
| D4 | Parameterization over free-form | Removes dependency on individual prompt-writing skill |
| D5 | T1→T5 token hierarchy | Matches attention mechanism weighting — reduces subject failures by 40–60% |
| D6 | Two-layer negative prompts | Enables targeted debugging without disrupting working parameters |
| D7 | 5 category presets | Hook type and emotional register must change structurally per business type |
| D8 | End card never AI-generated | QR scanability and logo precision cannot be guaranteed by generative models |
| D9 | Silence on end card | Cognitive bandwidth for QR scan task cannot be shared with audio processing |
| D10 | System starts from URL | No manual creative input — scales to any advertiser with a website |

---

## Example Output

See [`examples/the_ordinary/`](examples/the_ordinary/) for a complete production JSON generated by Gemini 3 Pro for The Ordinary Ascorbyl Glucoside Solution 12% — including all 5 scene prompts, negative layers, Google Lyria music structure, and Google Chirp voiceover script.

---

## Tools

| Tool | Role |
|------|------|
| Gemini 3 Pro | Orchestrator — reads brief, selects preset, assembles all prompts |
| Nano Banana Pro | Reference frame generation (still images) |
| Veo 3 | Video generation per scene |
| Google Lyria | Music — 6-zone timecoded structure |
| Google Chirp | Voiceover — per-line delivery notes |

---

## Documents

Full framework documentation available as formatted .docx files:

1. Overall Approach & Decisions
2. Generic Creative Brief (template)
3. The Ordinary Creative Brief (example)
4. Storyboard Strategy Framework
5. Gemini System Prompt
6. Gemini Output Example — The Ordinary

---

*CTV Ad Generation Framework · v1.0 · March 2026*
