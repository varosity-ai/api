---
title: "Using the CLI"
description: "Install @varosity/cli; render videos and music from the terminal."
surface: cli
triggers: ["cli", "headless", "scripts"]
difficulty: intermediate
estimatedMinutes: 6
---

# Using the CLI

The CLI is for the cases where the browser is too slow or too closed —
agentic flows, headless rendering, automation pipelines.

## Install

```bash
npm install -g @varosity/cli
varosity --version
```

## Pair with your account

```bash
varosity login
# → opens https://varosity.ai/app/keys/api-keys
# → paste your vsk_… token when prompted
```

Token is stored at `~/.varosity/config.json` (mode 0600). Never commit
this file.

## Common commands

### Render one video

```bash
varosity generate video kling-3.0-replicate \
  --prompt "wide cinematic establishing shot of a desert at golden hour" \
  --duration 6 \
  --out shot1.mp4
```

The CLI submits, polls every few seconds, and downloads when done.

### Generate a voiceover

```bash
varosity voices list --json | jq -r '.[].providerVoiceId' | head -3
# → 21m00Tcm4TlvDq8ikWAM
# → ErXwobaYiN019PkySvjV
# → EXAVITQu4vr4xnSDxMaL

varosity generate voice \
  --text "Welcome back. Here's what's new this week." \
  --voice 21m00Tcm4TlvDq8ikWAM \
  --out vo.mp3
```

### Background music

```bash
varosity generate music elevenlabs-music \
  --prompt "cinematic synth, slow build, hopeful" \
  --duration 30 \
  --out bg.mp3
```

### Smart Route

```bash
varosity route suggest --shot "talking head close-up with synced lips" --json
```

### Watch a long render

```bash
varosity generate video veo-3.1 --prompt "..." --duration 8
# → returns jobId
varosity job wait <jobId>
# blocks until done, prints progress, downloads if --out was set
```

## Agent mode

`--json` on any command makes the output stable and machine-parseable.
For Claude Code / Cursor / any MCP host, run:

```bash
varosity mcp
```

…which prints the JSON snippet to drop into Claude Desktop's
`claude_desktop_config.json`. The MCP endpoint exposes 11 tools (see
the agent-mode guide).

## Override the API base

```bash
VAROSITY_API_BASE=https://staging.varosity.ai varosity models list
```

Useful for staging environments or self-hosted instances.
