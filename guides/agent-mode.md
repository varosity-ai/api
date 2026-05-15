---
title: "Agent mode (Claude / Cursor / MCP)"
description: "Drive Varosity from any MCP host: 11 tools, bearer auth, JSON-RPC over Streamable HTTP."
surface: cli
triggers: ["claude desktop", "cursor", "mcp", "agent"]
difficulty: intermediate
estimatedMinutes: 7
---

# Agent mode

Varosity ships an MCP server at `/api/mcp`. Any MCP host (Claude Desktop,
Cursor, custom clients via the official SDK) can drive every important
capability without a browser.

## Quick setup

1. Issue a token at `/app/keys/api-keys` (label it for the host —
   "Claude Desktop on macbook").
2. In your MCP host's config, add:

```json
{
  "mcpServers": {
    "varosity": {
      "url": "https://varosity.ai/api/mcp",
      "transport": "streamable-http",
      "headers": {
        "Authorization": "Bearer vsk_<paste-here>"
      }
    }
  }
}
```

For Claude Desktop, the file lives at:
`~/Library/Application Support/Claude/claude_desktop_config.json` (macOS)

3. Restart the host.
4. The agent now has access to the Varosity tool surface.

## Tools exposed

The MCP surface is **curated**, not a wrapper around the entire API. 11
tools, deliberately:

| Tool             | What it does                                      |
|------------------|---------------------------------------------------|
| `list_models`    | Filter the registry by kind (video/voice/music)   |
| `list_voices`    | Your saved voice library                          |
| `list_projects`  | Recent projects                                   |
| `get_project`    | Single project + shot list                        |
| `create_project` | New empty project                                 |
| `generate_video` | Submit a video render job; returns jobId          |
| `generate_voice` | Synthesize speech via ElevenLabs                  |
| `generate_music` | Generate a music track                            |
| `suggest_model`  | Smart Route — rank models for a shot description  |
| `get_job`        | Status + output URL                               |
| `render_project` | Stitch project to one MP4                         |

## Hermes Skills for Multi-Shot Production

For sophisticated multi-shot workflows, load the **varosity-multi-shot-consistency** Hermes skill:

```json
{
  "skill": "varosity-multi-shot-consistency",
  "applies_to": ["music videos", "product reels", "brand campaigns", "interview sequences"],
  "enforces": "Locked reference image across all shots",
  "guides": "Two-phase workflow: Phase 1 (lock reference) → Phase 2 (generate shots in parallel)"
}
```

See [Multi-shot consistency with locked references](/docs/multi-shot-consistency) for full details.

## Other agent-readable surfaces

- [llms.txt](https://varosity.ai/llms.txt) — site overview
- [llms-full.txt](https://varosity.ai/llms-full.txt) — registry + guides + skills
- [agents.json](https://varosity.ai/.well-known/agents.json) — capability manifest
- [openapi.json](https://varosity.ai/api/openapi.json) — full REST spec

## A typical agent flow

> User: "Make a 15-second product reel for our new espresso machine. 3 shots."

1. Agent calls `suggest_model` for the establishing wide → ranks Kling 3.0.
2. Agent calls `create_project({title: "espresso reel"})` → projectId.
3. Agent calls `generate_video` 3 times across Kling/Veo/Seedance, returns jobIds.
4. Polls `get_job` until each lands.
5. Optional: `generate_music` for backing track, `generate_voice` for VO.
6. `render_project` for the final stitch.
7. Returns the MP4 URL to the user.

All of that, no browser.

## Limits + safeguards

- **Bearer tokens carry the user's full permissions.** Treat them like
  passwords. Revoke at `/app/keys/api-keys`.
- **Every tool runs through RLS** — an agent with your token can only
  see/touch your data, never another user's.
- **Rate limits** match the underlying providers'. The MCP server adds
  no synthetic limits in v2; budget caps on `/app/keys` apply.
