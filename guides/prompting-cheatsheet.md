---
title: "Prompting cheatsheet"
description: "Camera, lighting, and motion language that lands consistently across models."
surface: shot-inspector
triggers: ["prompting", "shot description"]
difficulty: intermediate
estimatedMinutes: 6
---

# Prompting cheatsheet

Frontier video models read cinematography vocabulary. Use it.

## Shot framing

- **Wide / establishing** — full environment, character small
- **Full** — character head-to-toe
- **Medium** — character waist-up
- **Medium close-up** — chest-up; the conversational frame
- **Close-up** — face fills frame
- **Extreme close-up** — single feature (eye, hand, object)
- **Over-the-shoulder** — POV-adjacent for dialogue
- **Bird's eye / overhead** — top-down
- **Low angle** — looking up at subject (heroic / threatening)

## Camera moves

- **Static / locked** — no movement
- **Push-in / dolly in** — moving toward subject
- **Pull-out / dolly out** — moving away
- **Pan** — horizontal pivot
- **Tilt** — vertical pivot
- **Tracking / follow** — moving alongside subject
- **Crane / boom** — vertical lift
- **Whip pan** — fast pan with motion blur
- **Dutch angle** — tilted horizon (tension)

## Lighting

- **Golden hour** — warm low sun, long shadows
- **Blue hour** — twilight, cool palette
- **Rembrandt** — single key light, triangle on the cheek
- **Practical** — visible in-frame light source (lamp, screen)
- **High key** — bright, low contrast (commercial)
- **Low key** — dark, high contrast (drama)
- **Backlit / silhouette** — light behind subject
- **Soft window light** — diffuse natural

## Motion + pacing

- **Slow-motion** / **120fps** — extreme slow-mo
- **Time-lapse** — accelerated time
- **Hand-held** — slight shake, organic feel
- **Steady-cam** — smooth despite movement
- **Static then push** — first second still, then push-in

## Texture / look

- **35mm film** / **16mm film** — grain, character
- **Anamorphic** — 2.39:1 with horizontal flares
- **Cinematic, shallow depth of field**
- **High contrast, color graded**

## Avoiding common failure modes

- **Don't over-specify motion in 5s shots.** Models compress motion;
  asking for "running through a forest, jumping over a log, then
  climbing a tree" in 5s yields a blur. Pick one motion beat.
- **Subject FIRST, then style.** Models read the first 30 tokens most
  closely. Lead with what's in frame.
- **One scene per shot.** Cuts within a shot are unreliable across all
  models. Use the storyboard for cuts.

## Templates that work

### Talking head
> *Medium close-up of [person], [emotion]. [Lighting]. Slight slow zoom-in. Cinematic, shallow depth of field, 35mm.*

### Product reveal
> *Overhead top-down shot of [product] on [surface], [lighting]. Slow downward dolly. Minimalist editorial.*

### Action beat
> *[Subject] [doing one action] in [environment], [time of day]. Tracking shot. Hand-held energy.*

### B-roll
> *[Setting] at [time], [lighting], [weather]. Slow [motion]. Quiet, observational.*
