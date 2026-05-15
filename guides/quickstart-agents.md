# Varosity for Agents: 10-Minute Quickstart

Get your agent generating videos in 10 minutes. This guide assumes you're using Claude Desktop, Cursor, Hermes, or another MCP-capable agent framework.

**Total time: < 10 minutes**

---

## Step 1: Get Your API Key (2 minutes)

1. Go to **https://varosity.ai/app/keys/api-keys**
2. Click **"Create new key"**
3. Copy the key (starts with `vsk_live_`)
4. Keep it safe — never share it

**Done.** You now have access to all Varosity tools.

---

## Step 2: Add Varosity to Your Agent Config (1 minute)

### Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "varosity": {
      "command": "npx",
      "args": ["-y", "@varosity/mcp-server"],
      "env": {
        "VAROSITY_API_KEY": "vsk_live_YOUR_KEY_HERE"
      }
    }
  }
}
```

Replace `vsk_live_YOUR_KEY_HERE` with your actual key.

### Cursor

Edit `.cursor/settings.json` (or use Cursor Settings):

```json
{
  "mcpServers": {
    "varosity": {
      "command": "npx",
      "args": ["-y", "@varosity/mcp-server"],
      "env": {
        "VAROSITY_API_KEY": "vsk_live_YOUR_KEY_HERE"
      }
    }
  }
}
```

### Hermes

Add to `~/.hermes/config.yaml`:

```yaml
mcp_servers:
  varosity:
    command: npx
    args:
      - "-y"
      - "@varosity/mcp-server"
    env:
      VAROSITY_API_KEY: "vsk_live_YOUR_KEY_HERE"
```

### Custom Node.js Agent

```javascript
import { MCPClient } from "@varosity/mcp-client";

const client = new MCPClient({
  apiKey: process.env.VAROSITY_API_KEY
});

const tools = await client.listTools();
```

---

## Step 3: Test Your Connection (2 minutes)

### Claude Desktop & Cursor

1. **Restart** Claude Desktop or Cursor (exit completely)
2. Open a new conversation
3. Ask: **"What tools from Varosity do you have access to?"**

Expected response:
```
I have access to the following Varosity tools:
- generate_video: Generate a video from a text prompt
- generate_voice: Generate speech from text
- generate_music: Generate music from a description
- list_models: See available models (Kling, Veo, Runway, etc.)
- suggest_model: Get model recommendation for your prompt
... (and 30+ more)
```

If you see this list, you're connected! ✅

### Hermes

```bash
hermes "What tools from Varosity do you have access to?"
```

---

## Step 4: Generate Your First Video (< 2 minutes)

### Via Chat (Claude Desktop / Cursor)

Just ask your agent:

> Generate a 5-second video of a cat playing a piano. The cat should look happy and be wearing sunglasses. Use whatever model is best.

Your agent will:
1. Use `suggest_model` to pick the best model
2. Call `generate_video` with your prompt
3. Poll `get_job` until the video is ready
4. Return the video URL

**Output:**
```
✓ Video generated!
Model: kling-1 (best for animals)
Duration: 5 seconds
Size: 12 MB
URL: https://videos.varosity.ai/...
Cost: $0.50
```

### Via Code (Hermes / Custom Agent)

```python
from hermes import agent

response = agent.chat(
    "Generate a 10-second video of a sunset over mountains. Make it cinematic."
)
# Agent handles generate_video + polling automatically
```

---

## Step 5: What's Next? (Optional)

### Try Advanced Patterns

**Multi-shot campaign:**
> Create a 3-shot product demo video: 
> Shot 1 - Hero shot of product on marble background
> Shot 2 - Close-up of key feature
> Shot 3 - Lifestyle shot of someone using it

**Music video:**
> Generate a 15-second music video of a DJ performing, set to upbeat electronic music

**Avatar (talking head):**
> Create a 30-second video of a person explaining how to use our API, with a nature background

**Brand consistency:**
> Generate 5 variations of this video using different models (Kling, Veo, Runway) so I can compare

### Learn More

- **[MCP Reference Architecture](./mcp-reference-architecture.md)** — How Varosity's MCP server works
- **[Agent Integration Guide](../agent-guide.md)** — Complete tool reference (35+ tools)
- **[Prompting Cheatsheet](./prompting-cheatsheet.md)** — How to write better video prompts
- **[Picking the Right Model](./picking-the-right-model.md)** — Kling vs. Veo vs. Runway vs. Sora
- **[Cookbook Examples](https://github.com/varosity-ai/cookbook)** — Runnable agent code

---

## Troubleshooting

### "Varosity tools not showing up"

**If your agent doesn't see Varosity tools:**

1. ✅ Did you restart your agent? (Exit and reopen)
2. ✅ Is your API key correct? (Check it starts with `vsk_live_`)
3. ✅ Is the MCP config in the right place?
   - Claude: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Cursor: `.cursor/settings.json` or Cursor Settings
   - Hermes: `~/.hermes/config.yaml`

**If still stuck:** See [Common Integration Issues](../agent-guide.md#troubleshooting).

### "Invalid API key" error

1. Go to **https://varosity.ai/app/keys/api-keys**
2. Create a new key
3. Replace the old one in your config
4. Restart your agent

### "Rate limited — too many requests"

If you hit rate limits:
- Your agent will automatically retry with exponential backoff
- Standard limits: 100 videos/day on starter plan
- Contact support or upgrade plan if you hit this regularly

### "Video generation failed / timeout"

Most videos render in 10–30 seconds. If it takes longer:
1. Your agent will keep polling
2. This is normal for complex prompts
3. If > 2 minutes, the video probably errored — try again with a simpler prompt

**Get help:** Ask a question in https://github.com/varosity-ai/issues-public/discussions

---

## Key Concepts

### What's an MCP Server?

MCP (Model Context Protocol) is a standard for agents to discover and call tools. The Varosity MCP server exposes all our video/voice/music tools to your agent.

### Token Usage & Costs

- **Free tier:** $0 — test with sample keys
- **Pay-as-you-go:** $0.10–$0.15 per second of video (depends on model)
- **Example:** 10-second video = $1.00–$1.50
- **Billing:** Monthly invoice or prepaid credits

See [pricing](https://varosity.ai/pricing) for details.

### BYOK (Bring Your Own Keys)

You can use your own Kling, Veo, or Runway API keys if you have them:

1. Go to **Settings → Keys**
2. Add your provider API keys
3. Varosity will route jobs to your accounts
4. Billing goes to your provider, not Varosity

See [BYOK Setup](./byok-setup.md) for step-by-step.

### Model Selection

Varosity currently supports:
- **Kling 3.0 Pro** — Best for cinematic, realistic video
- **Veo 2** — Great for style transfer, creative effects
- **Runway Gen-4.5** — Fast, good quality
- **Sora 2** — High-fidelity, still in waitlist
- **AudioCraft** — AI music generation
- **ElevenLabs** — Professional voice synthesis

Use `/suggest_model` or see [Picking the Right Model](./picking-the-right-model.md) to choose.

---

## One-Liner Examples

Copy-paste these into your agent:

**Simple video:**
```
Generate a 5-second video of a dog running through a field at sunset.
```

**With model hint:**
```
Generate a 10-second cinematic video of a waterfall at night using Kling 3.0.
```

**Multi-shot campaign:**
```
Create a 3-shot product video: (1) Hero shot, (2) Feature close-up, (3) Lifestyle. Use Veo for shot 1 and Kling for shots 2-3.
```

**With voice over:**
```
Generate a 30-second educational video about climate change, narrated by a professional voice.
```

**Brand consistent series:**
```
Create 5 variations of a product launch video (one per model) so I can see which looks best.
```

---

## Success Checklist

- ✅ API key obtained and stored securely
- ✅ MCP config added to your agent
- ✅ Agent restarted and Varosity tools show up
- ✅ Test prompt generated successfully
- ✅ Video downloaded and viewed

**You're ready to build!**

---

## Next Steps

1. **Build an agent workflow** — See the [Cookbook](https://github.com/varosity-ai/cookbook) for working examples
2. **Optimize prompts** — Read the [Prompting Cheatsheet](./prompting-cheatsheet.md)
3. **Track costs** — Use the [Billing & Credits](../agent-guide.md#billing) section of the agent guide
4. **Get feedback** — Post in [GitHub Discussions](https://github.com/varosity-ai/issues-public/discussions)

---

**Questions?**

- 📖 [Full docs](https://docs.varosity.ai)
- 💬 [GitHub Discussions](https://github.com/varosity-ai/issues-public/discussions)
- 🐛 [Report issues](https://github.com/varosity-ai/issues-public/issues)
- 🌐 [varosity.ai](https://varosity.ai)

**Time spent:** ~9 minutes ⏱️
