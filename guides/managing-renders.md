---
title: "Managing renders + costs"
description: "Where renders live, how to download, and how to keep BYOK costs under control."
surface: renders
triggers: ["renders", "costs", "billing"]
difficulty: beginner
estimatedMinutes: 4
---

# Managing renders + costs

Every stitched MP4 lives at `/renders` with a download link, the
duration, and the per-render BYOK cost in dollars. There's no markup —
what fal/ElevenLabs/etc. bill is what you see.

## Per-render cost

Each shot's `cost_cents` is stamped at completion based on the
registry's `pricePerSec` × duration. The `/renders` table sums them per
project for the total.

> Heads-up: provider pricing changes. The registry is updated in
> migrations; if your in-flight bill diverges from what we display,
> the provider's number wins.

## Per-month cost cap

Settings → Keys → each provider row has a **Budget cap** in dollars.
When the month-to-date spend hits the cap, all further generations for
that provider 402 with `BudgetExceededError`. Reset on the 1st.

Set it to whatever you can lose without thinking. Default: $0 (no
cap). Recommended: $25 to start.

## Cleanup

Renders are kept indefinitely. Delete from the table when you don't
need them. Storage charges are negligible on the BYOK plan; we don't
charge for storage in v2.

## Why a render failed

The shot card shows the provider's error message inline (e.g.
`fal balance exhausted`, `Invalid prompt: must be < 2000 chars`). For
deeper failures, check the inspector's error banner — it surfaces the
full provider response.
