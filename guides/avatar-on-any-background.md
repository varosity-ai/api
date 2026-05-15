---
title: "Avatar on any background"
description: "Composite a talking head on a Veo / Kling / Runway background."
surface: shot-inspector
triggers: ["avatar mode", "talking head"]
difficulty: intermediate
estimatedMinutes: 8
---

# Avatar on any background (the HeyGen-killer)

HeyGen ships avatars on a fixed pipeline. Varosity composites a talking
head on **any** of the frontier video models. Three modes:

## Mode: Off
Ignore the attached avatar even if one is set. Useful when iterating on
the BG without reburning OmniHuman credits.

## Mode: Full frame
OmniHuman replaces the shot entirely. Good for plain talking-heads.
Required: avatar photo + audio clip (or TTS script).

## Mode: Overlay (PiP)
The killer feature.

1. Background renders via your chosen text-to-video model (Veo for lip
   sync if dialogue, Kling for cinematic, Seedance for audio-native…).
2. OmniHuman renders the talking head from a photo + audio in parallel.
3. Once both finish, Varosity runs `ffmpeg.wasm` in your browser to
   overlay the avatar in the chosen corner at the chosen size.
4. The composite replaces the shot's render_url. Stitches into the final
   MP4 like any other shot.

## How to set it up

1. Upload a high-resolution front-facing photo at `/avatars`.
2. In the shot inspector, expand **Avatar**.
3. Pick the avatar.
4. Either upload an audio clip OR switch to **From script** and paste
   text — ElevenLabs synthesizes (BYOK).
5. Switch **Layer mode** to **Overlay**.
6. Choose corner + size in the position picker.
7. Hit **Render this shot**. Two jobs go out; the compositor fires when
   both land.

## Tips

- **Overlay corner** matters more than you think. Bottom-right reads as
  presenter; top-right as branding/sponsorship; top-left as primary
  caller. Pick deliberately.
- **Avatar size 25–30%** is the sweet spot for most shots. Smaller
  reads as ambient; larger steals focus from the BG.
- **Audio length sets shot duration in Full mode.** OmniHuman's output
  is bound by the audio length; ignore the duration slider.
