---
title: "BYOK setup per provider"
description: "Where to get each provider's key + how to add it to Varosity."
surface: settings-keys
triggers: ["add a key", "BYOK", "provider key"]
difficulty: beginner
estimatedMinutes: 5
---

# BYOK setup per provider

You bring the keys; Varosity routes the calls. Every key is encrypted at
rest with AES-256-GCM. Plaintext never leaves the server, never gets
logged, never appears in API responses.

## fal.ai (recommended first)

Unlocks: Veo 3.1, Kling 3.0 Pro, Seedance 4.5, OmniHuman 1.5, MiniMax Music, Lyria 2.

1. [fal.ai/dashboard/keys](https://fal.ai/dashboard/keys) → **Create key**.
2. Add credits at [fal.ai/dashboard/billing](https://fal.ai/dashboard/billing).
3. Settings → Keys → fal.ai row → paste → **Test** → **Save**.

## Replicate

Unlocks: Kling 3.0 (alt route), Wan 2.5, Pika 2.2.

1. [replicate.com/account/api-tokens](https://replicate.com/account/api-tokens) → **Create token**.
2. Settings → Keys → Replicate → paste → Save.

## ElevenLabs

Unlocks: TTS (every voice in your library), voice cloning, ElevenLabs Music.

1. [elevenlabs.io/app/settings/api-keys](https://elevenlabs.io/app/settings/api-keys) → **Create**.
2. Make sure your subscription tier allows API access (Starter and up).
3. Settings → Keys → ElevenLabs → paste → Save.

## OpenAI (Sora 2)

Unlocks: Sora 2 — *currently waitlist-only API.*

Add the key now; Varosity flips the model from "unavailable" to live the
moment OpenAI opens API access.

1. [platform.openai.com/api-keys](https://platform.openai.com/api-keys) → **Create**.
2. Settings → Keys → OpenAI → paste → Save.

## Runway

Unlocks: Gen-4.5 direct (motion brush, camera control).

1. [docs.dev.runwayml.com](https://docs.dev.runwayml.com/) → API key in your Runway account settings.
2. Settings → Keys → Runway → paste → Save.

## Google AI Studio (Veo direct)

Unlocks: Veo 3.1 via the Gemini API (lower latency than fal route).

1. [aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey) → **Create API key**.
2. Settings → Keys → Google → paste → Save.

## Fish Audio

Unlocks: cheaper multilingual TTS.

1. [fish.audio/go-api](https://fish.audio/go-api) → API key.
2. Settings → Keys → Fish Audio → paste → Save.

## Cartesia

Unlocks: ultra-low-latency TTS for real-time use.

1. [play.cartesia.ai/keys](https://play.cartesia.ai/keys) → **Create**.
2. Settings → Keys → Cartesia → paste → Save.

## Test before you save

Every row has a **Test** button. It hits a lightweight endpoint at the
provider, surfaces success/failure with the message. Save only after
green.

## Rotate / revoke

Click the row → **Rotate** prompts for a new key (replaces in place) or
**Revoke** deletes it. Either action is immediate.
