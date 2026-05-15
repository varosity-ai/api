---
title: "Chaining clips across models"
description: "Use the right model for each shot type, then stitch them into one MP4."
surface: storyboard-canvas
triggers: ["new project", "empty canvas"]
difficulty: beginner
estimatedMinutes: 10
---

# Chaining clips across models

The pitch: no single model is best at everything. Veo 3.1 owns lip-sync,
Kling 3.0 owns cinematic motion, Seedance owns audio-native generation,
Runway owns camera control. Varosity lets you mix-and-match per shot.

## A simple 4-shot example

| Shot | Type            | Best model         | Why                            |
|------|-----------------|--------------------|--------------------------------|
| 01   | Wide establishing | Kling 3.0 Pro    | cinematic motion, cheap        |
| 02   | Talking close-up  | Veo 3.1          | best lip-sync, native audio    |
| 03   | Action beat       | Seedance 4.5     | audio-native, multi-shot intent|
| 04   | Hero close-up     | Runway Gen-4.5   | camera control + motion brush  |

Set up:
1. Create a project.
2. Click **+ Add your first shot**, pick Kling, write the wide.
3. **+ Insert after**, pick Veo for the dialogue, paste your line.
4. Repeat for shots 03 and 04.
5. Hit **Render this shot + all downstream** to fan out renders.
6. Once everything's done, **Render final** stitches into one MP4.

## When you don't know which model to pick

Click the **Smart Route** chip in the inspector. It sends your shot
description to a routing LLM (OpenRouter / Claude Haiku) which ranks the
top 3 candidates with reasoning. The chip pulls exclusively from your
configured providers — it'll never recommend a model you can't use.

## Cost discipline

The cost line in the inspector estimates per-shot at the registry's
`pricePerSec`. The dashboard's Renders tab shows the total spent per
final cut. There's no markup — what fal/Runway/etc. bill is what you pay.

## See also

- [Picking the right model](/docs/picking-the-right-model)
- [Prompting cheatsheet](/docs/prompting-cheatsheet)
- [Background music and ducking](/docs/background-music-and-ducking)
