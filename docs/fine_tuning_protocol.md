# Fine-Tuning & Iteration Protocol

4-stage production protocol designed to catch errors at the cheapest possible point.

**Core principle:** The cost of an error grows exponentially at each stage. A missing parameter caught at Stage 1 costs zero. The same error caught after generating 5 video clips costs the entire generation budget for that session.

---

## Stage Overview

```
Stage 1 — Structural Validation     cost to catch error: zero
        ↓
Stage 2 — Reference Frame Calibration    cost: one image generation
        ↓
Stage 3 — Scene-by-Scene Video Iteration     cost: one scene regeneration
        ↓
Stage 4 — Assembly Continuity Check     cost: one scene regeneration
```

---

## Stage 1 — Structural Validation

**When:** Before sending a single prompt to any generation tool.

**What to check:**

| Check | Pass condition | Fail action |
|-------|---------------|-------------|
| Scene durations | All 5 scenes sum to exactly 30 seconds | Stop. Fix timing before proceeding. |
| Required parameters | All REQUIRED params populated and non-empty | Stop. Return structured error with failing field. |
| Business Type | Matches one of the 5 category presets | Stop. Clarify with advertiser. |

> A prompt sent to Veo 3 with an empty `{{PRODUCT_NAME}}` will hallucinate a product. It will not return an error. The cost of catching this at Stage 1 is zero. The cost of catching it after 5 generated clips is the entire generation budget for that session.

**All three checks must pass before proceeding to Stage 2.**

---

## Stage 2 — Reference Frame Calibration

**When:** Before generating any video. After Stage 1 passes.

**Tool:** Nano Banana Pro — still images only.

**What to generate:** One reference frame per scene (5 frames total) + one end card background.

**Evaluation criteria per frame:**

| Criterion | Check |
|-----------|-------|
| Brand color | Is the brand's primary color dominant in the grade? |
| Subject separation | Is subject-to-background separation clean? |
| Lighting direction | Does it match the Kelvin temperature specified in T4? |
| Talent casting | Does talent match `{{TALENT_DESCRIPTOR}}`? |

**Decision rule:**

- **3 or more frames pass** → lock those seeds, proceed to Stage 3.
- **Frame fails** → identify the single parameter that caused the failure. Adjust only that parameter. Regenerate only that frame.

> Do not change multiple parameters simultaneously on a failing frame — you will not know which one fixed the problem.

**Why this stage exists:**

A wrong color grade caught at Stage 2 costs one image generation. Caught after Veo 3 has generated all five clips, it requires reprocessing every scene. Running Nano Banana Pro first is not optional overhead — it reduces total generation iterations by approximately half.

---

## Stage 3 — Scene-by-Scene Video Iteration

**When:** After Stage 2 seeds are locked.

**Tool:** Veo 3.

**Generation order:** S1 → S2 → S3 → S4 → S5 (narrative order). Continuity errors in talent appearance or color grade are caught early this way.

### The 60% Rule

After each scene generation, evaluate what percentage of the frame is behaving as intended:

| Score | Decision | Action |
|-------|----------|--------|
| ≥ 60% correct | `refine` | Lock seed. Modify **only** the single failing parameter. |
| < 60% correct | `regenerate` | Set `seed_id: null`. Revise prompt broadly. Do not attempt surgical fixes. |
| ≥ 3 iterations AND < 80% accurate | `restructure` | Rewrite T1 Subject+Action entirely. |

> Attempting to patch a fundamentally wrong generation with incremental fixes wastes iteration budget. If less than 60% is correct — regenerate from scratch.

### Common single-parameter fixes (refine)

| Failure | Fix |
|---------|-----|
| Camera movement ignored | Replace movement term with spatial description: `camera moves from 2m to 0.5m distance from subject` |
| Wrong lighting direction | Add explicit Kelvin value and practical source description to T4 |
| Talent expression off | Add scene-specific negative entry: copy the exact failure description verbatim |
| Brand color wrong | Specify hex code in T4 grade description |

### Why T1 carries 35% of prompt weight

If a scene has not reached 80% accuracy after 3 iterations, the problem is structural — not stylistic. T1 (Subject + Action) carries approximately 35% of the model's attention weight. Three failed iterations with the same T1 means the subject description itself is the problem. Rewrite T1 completely with a different compositional approach for the same narrative function.

---

## Stage 4 — Assembly Continuity Check

**When:** After all 5 clips are generated.

**What to review:** Play all 5 clips in sequence at full speed. Check three continuity requirements:

### 1. Color grade consistency

The same brand color must read as dominant across all scenes. Veo 3 generates scenes independently — color grade can drift between clips.

**If drift is significant:** Regenerate the outlier scene using the most accurate scene's seed as a visual anchor note in the prompt.

### 2. Talent continuity

The same person must appear recognizably across S2, S4, and S5. Because scenes are generated independently, talent appearance — face, hair, clothing — can change between clips.

**If drift is significant:** Regenerate the outlier scene. Reference the most accurate scene's physical description in T1 of the drifting scene.

### 3. Pacing rhythm

Play the assembled cut against the Google Lyria music track. Beat-sync cuts in S4 must land on musical beats.

**If cuts don't land on beats:** The editor adjusts clip speed ±10% rather than regenerating. Do not regenerate for a pacing issue that can be fixed in the NLE.

---

## Iteration Feedback Schema

When the pipeline returns a failed scene for revision, it sends a structured feedback object:

```json
{
  "iteration_feedback": {
    "scene_id": 2,
    "iteration": 1,
    "score": 0.45,
    "passed_parameters": ["composition", "environment"],
    "failed_parameters": [
      { "parameter": "skin_texture", "issue": "plastic, airbrushed appearance" },
      { "parameter": "lighting_direction", "issue": "measured 3200K, brief specifies 5500K" }
    ],
    "seed_id": null,
    "decision": "regenerate"
  }
}
```

**Response rules:**

1. Return the full scene object — not a partial patch. The pipeline replaces the entire scene object.
2. Increment `iteration` by 1.
3. For `refine`: populate `seed_id` from feedback. Change only the tokens addressing the failed parameter.
4. For `regenerate`: set `seed_id: null`. Add every `issue` string from `failed_parameters` verbatim into the negative prompt.
5. For `restructure`: rewrite T1 completely. Set `seed_id: null`.
6. Never remove items from the global negative layer — only add.
7. Update **both** `nano_banana_prompt` and `veo3_prompt` — they must stay synchronized.
8. If the same parameter fails across 3+ scenes → fix ALL affected scenes, not just the flagged one.

**Always include `changes_made` array in the response:**

```json
{
  "iteration_response": {
    "scene_id": 2,
    "iteration": 2,
    "revised_scene": { "..." },
    "changes_made": [
      "T1: added 'visible pores, natural skin imperfections, no foundation'",
      "T4: changed '3200K warm' to '5500K neutral daylight'",
      "nano_banana_negative: added 'plastic skin, airbrushed appearance'",
      "veo3_negative: added 'plastic skin, airbrushed appearance'"
    ]
  }
}
```

The `changes_made` array creates a traceable audit trail. Quote the exact tokens added, removed, or modified — do not paraphrase.

---

## Audio Iteration

If feedback targets audio (Google Lyria or Google Chirp), the same rules apply with audio-specific parameters:

- **Lyria feedback** references density zones — adjust the affected zone's description only.
- **Chirp feedback** references delivery or pacing — adjust `speaking_rate` or delivery instruction for the affected line.

Return only the revised `audio` object — do not regenerate scenes.

---

## Maximum Iterations Reference

| Stage | Maximum before escalating |
|-------|--------------------------|
| Stage 2 — single frame | 2 iterations before restructuring T1 |
| Stage 3 — single scene | 3 iterations before `restructure` decision |
| Stage 3 — systematic failure (3+ scenes same parameter) | Fix all scenes simultaneously |

---

*Fine-Tuning & Iteration Protocol · v1.0 · part of the CTV Ad Generation Framework*
