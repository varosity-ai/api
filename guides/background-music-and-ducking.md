---
title: "Background music + auto-ducking"
description: "Add a soundtrack to a project; voice ducks the music automatically."
surface: music
triggers: ["soundtrack", "background music"]
difficulty: beginner
estimatedMinutes: 5
---

# Background music + auto-ducking

## Pick a model

Three options on the `/music` page:

| Provider           | When to use                                  |
|--------------------|----------------------------------------------|
| **ElevenLabs Music** | Commercial work — licensed training data.  |
| MiniMax            | Cheap, fast, good for quick comps.           |
| Lyria 2            | Long-form instrumental composition.          |

Suno is feature-flagged off by default — there's no public API and the
licensing is unclear. ElevenLabs Music is the safe bet for paid work.

## Generate a track

`/music` → composer → write a vibe (e.g. *"cinematic synth, slow build,
hopeful, sparse percussion"*) → set duration → **Generate**.

Sync providers (ElevenLabs) drop the track immediately. Async providers
(fal-routed) queue and poll; the track appears in the library when ready.

## Attach to a project

Open the project → **Music** panel (top of the inspector when no shot is
selected) → pick a track from the library → save.

The stitch pipeline mixes the track under your video automatically.

## Auto-ducking

When the project has voice/dialogue audio (TTS attached to any shot, or
a Voice node), the music **ducks to -12dB** during voice and rides full
volume between. Implemented with `ffmpeg`'s `sidechaincompress` filter.

Toggle off in the project Music panel if you want flat music.

## Tips

- **Match track duration to total shot length.** A 30s reel needs a 30s
  track — overflow clips at the end of the final stitch.
- **Genre tags help.** Adding "no vocals" or "instrumental only" in the
  prompt prevents lyrics that fight your voiceover.
- **Test the duck.** Render final once with ducking enabled, once with
  it off; pick whichever the video calls for.
