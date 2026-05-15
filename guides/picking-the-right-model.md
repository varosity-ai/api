---
title: "Picking the right model"
description: "Strengths and weaknesses cheat sheet across every video, voice, and music model."
surface: shot-inspector
triggers: ["model selection", "which model"]
difficulty: beginner
estimatedMinutes: 4
---

# Picking the right model

This is also rendered live in the **Smart Route** chip in the inspector
based on your shot description. The cheat sheet below is the static map.

## Video models

### Veo 3.1
- **Pick when**: dialogue close-ups, lip-sync matters, you want native audio.
- **Skip when**: you need camera control or video-to-video.
- **Cost**: ~\$0.15/s.

### Kling 3.0 Pro
- **Pick when**: cinematic motion, multi-shot consistency, mid-budget cinematic action.
- **Skip when**: lip-sync matters more than motion.
- **Cost**: ~\$0.10/s direct, ~\$0.09/s via Replicate.

### Seedance 4.5
- **Pick when**: you want the audio + video generated together, multi-shot from one prompt.
- **Skip when**: you need fine motion control.
- **Cost**: ~\$0.14/s.

### Runway Gen-4.5
- **Pick when**: motion brush, camera path control, video-to-video editing.
- **Skip when**: you want native audio (Runway is video-only).
- **Cost**: ~\$0.12/s.

### Wan 2.5 (Replicate)
- **Pick when**: realistic motion (product shots), fast turnaround.
- **Skip when**: you want stylized cinematography.
- **Cost**: ~\$0.07/s.

### Pika 2.2 (Replicate)
- **Pick when**: creative transitions, ingredient blends, stylized motion.
- **Skip when**: you need photoreal output.
- **Cost**: ~\$0.08/s.

### OmniHuman 1.5
- **Pick when**: talking-head avatar from a single photo.
- **Skip when**: you don't have an audio clip.
- **Cost**: ~\$0.08/s.

### Sora 2
- **Pick when**: most cinematic shot, complex physics — *if you have access*.
- **Skip when**: API is waitlist-only (still as of 2026-04).

## Voice models

### ElevenLabs Multilingual v2
- **Pick when**: highest-quality voiceover, voice cloning needed.
- **Cost**: ~\$0.005/s.

### ElevenLabs v3 (Alpha)
- **Pick when**: dialogue / two-speaker scripts, max expressiveness.
- **Skip when**: alpha quality variance is a deal-breaker.

## Music models

### ElevenLabs Music
- **Pick when**: commercial work, licensed audio required.
- **Cost**: ~\$0.02/s.

### MiniMax Music
- **Pick when**: cheap quick comps, demo reels.
- **Cost**: ~\$0.01/s.

### Lyria 2
- **Pick when**: instrumental composition, longer-form tracks.
- **Cost**: ~\$0.015/s.

## Use the Smart Route

In doubt? Open the inspector → **Smart Route** chip → describe the shot
in plain English → it ranks the top 3 with reasoning. Pulls only from
your configured providers.
