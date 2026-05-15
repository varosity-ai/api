---
title: "Save as workflow template"
description: "Turn a project into a reusable template; instantiate from UI, CLI, or MCP."
surface: storyboard-canvas
triggers: ["template", "workflow", "reusable"]
difficulty: intermediate
estimatedMinutes: 5
---

# Save as workflow template

Built a project structure you'll want to reuse? Save it as a workflow
template. The graph (shots, models, durations, music attachment, voice
attachment) gets snapshotted; outputs are NOT included.

## From the studio

Open a project → top-right overflow menu → **Save as template**.

- **Name** — the human-friendly title (e.g. "Founder podcast promo")
- **Description** — what this template is for
- **Inputs** — variables the user fills in when instantiating
  (e.g. `host_name`, `episode_title`, `cta_url`)
- **Public** — opt-in to share at `/workflows`

## Instantiating

### From the UI
Dashboard → **Start from template** → pick yours from the grid.

### From the CLI

```bash
varosity workflow run founder-podcast-promo \
  --input host_name="Jon Kludt" \
  --input episode_title="Building Varosity" \
  --out promo.mp4
```

### From MCP

```
{ "tool": "run_workflow", "args": {
  "slug": "founder-podcast-promo",
  "inputs": { "host_name": "Jon Kludt", "episode_title": "Building Varosity" }
}}
```

## Why this matters for agents

A workflow template turns a multi-step Varosity flow into a one-line
call from any agent. Combined with the MCP server, an autonomous ops
agent can produce a full marketing reel from a topic spec without ever
opening the studio.

## Versioning

Templates are immutable once published — edits create a new version
(`founder-podcast-promo@2`). Old runs continue to work against the
version they were started with.
