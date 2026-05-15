---
title: "Voice over your video"
description: "Add narration with ElevenLabs, Fish Audio, or Cartesia."
surface: voices
triggers: ["voice over", "narration", "tts"]
difficulty: beginner
estimatedMinutes: 6
---

# Voice over your video

Three providers, one workflow. Pick the right one:

| Provider     | Strength                              | Pricing tier    |
|--------------|---------------------------------------|-----------------|
| ElevenLabs   | Highest quality + voice cloning       | $$              |
| Fish Audio   | Multilingual, cheaper                 | $               |
| Cartesia     | Ultra-low-latency (real-time use)     | $$              |

## Pull your voice library

1. Add the provider's key in **Settings → Keys**.
2. Visit `/voices`. Hit **Sync** on the provider's row. Your library
   lands in the grid.
3. Click ▶ on any voice to preview.

## Generate audio for a shot

In the shot inspector, expand **Avatar**. The audio row has two tabs:

- **Upload** — drop an mp3/wav you already have.
- **From script** — paste text, pick a voice, hit Generate. ElevenLabs
  synthesizes via your key, audio uploads to Storage, attached to the
  shot in one click.

## Voice cloning (ElevenLabs only)

`/voices` → Coming-soon UI for v2. Until then, clone via ElevenLabs's UI
and `/voices` will sync the cloned voice on next refresh.

## Tips

- Keep narration scripts under 30 seconds per shot. Longer reads stretch
  past video durations and clip awkwardly.
- For dialogue (lip-sync to the on-screen face), use **Veo 3.1 + Full
  avatar mode** instead of TTS over a separate clip. Veo synthesizes
  the lip motion from the audio.
- TTS audio attached to a shot becomes its primary audio track in the
  stitch. Background music ducks under it automatically — see the music
  guide.
