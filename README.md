# Varosity API Reference

Complete guides and documentation for integrating Varosity into your agents.

## Quick Start

- **[Agent Integration Guide](./agent-guide.md)** — How to use Varosity with Claude Desktop, Cursor, Hermes, and custom agents
- **[Platform Keys Setup](./platform-keys-setup.md)** — Configure BYOK keys for Kling, Veo, Runway, ElevenLabs, and more

## Guides

| Guide | Purpose |
|-------|---------|
| [Quick Start](./guides/quick-start.md) | Get video in < 10 minutes |
| [Using the CLI](./guides/using-the-cli.md) | Command-line interface basics |
| [Agent Mode](./guides/agent-mode.md) | Deploy Varosity as an MCP server |
| [Video Agent SDK](./guides/agent-video-sdk.md) | Advanced SDK patterns |
| [Picking the Right Model](./guides/picking-the-right-model.md) | Veo vs. Kling vs. Runway selection guide |
| [Prompting Cheatsheet](./guides/prompting-cheatsheet.md) | Optimize your video prompts |
| [Multi-Shot Storyboards](./guides/multi-shot-storyboard.md) | Orchestrate multi-clip renders |
| [Director Mode](./guides/director-mode.mdx) | Plan cinematography with Claude |
| [Avatar (PiP) Mode](./guides/avatar-on-any-background.md) | Talking-head videos over AI backgrounds |
| [Voice Over Your Video](./guides/voice-over-your-video.md) | Add ElevenLabs TTS |
| [Background Music & Ducking](./guides/background-music-and-ducking.md) | Audio mixing strategies |
| [Workflows](./guides/workflows.md) | Common video generation patterns |
| [Managing Renders](./guides/managing-renders.md) | Cancel, retry, compare models |

## MCP Connection

For MCP hosts (Claude Desktop, Cursor, Hermes):

```json
{
  "mcpServers": {
    "varosity": {
      "command": "npx",
      "args": ["-y", "@varosity/mcp-server"],
      "env": {
        "VAROSITY_API_KEY": "vsk_live_..."
      }
    }
  }
}
```

Get your API key at [varosity.ai/app/keys/api-keys](https://varosity.ai/app/keys/api-keys).

## Examples

See the [Varosity Cookbook](https://github.com/varosity-ai/cookbook) for runnable agent examples:

- Brand campaign generator (multi-shot storyboard)
- Product reel from description
- News headline to short-form video

## Support

- 📖 [Full docs](https://docs.varosity.ai)
- 💬 [GitHub Discussions](https://github.com/varosity-ai/issues-public/discussions)
- 🐛 [Report issues](https://github.com/varosity-ai/issues-public/issues)
- 🎯 [View roadmap](https://github.com/varosity-ai/issues-public/projects)

---

**License:** MIT  
**Varosity.ai:** https://varosity.ai
