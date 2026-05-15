# Varosity Agent Guide

Reference for AI agents (Hermes, Claude, custom orchestrators) working with the Varosity MCP API.

---

## Authentication

**Endpoint:** `https://varosity.ai/api/mcp`  
**Protocol:** JSON-RPC 2.0  
**Auth:** `Authorization: Bearer vsk_...`

```python
headers = {
    "Authorization": f"Bearer {api_token}",
    "Content-Type": "application/json"
}

def call(tool, args):
    r = requests.post("https://varosity.ai/api/mcp", json={
        "jsonrpc": "2.0",
        "method": "tools/call",
        "params": {"name": tool, "arguments": args},
        "id": int(time.time() * 1000) % 1_000_000
    }, headers=headers, timeout=30)
    content = r.json().get("result", {}).get("content", [{}])[0].get("text", "")
    return json.loads(content) if content else {}
```

### Required Scopes (by tool)

| Scope | Tools |
|-------|-------|
| `projects:read` | `list_projects`, `get_project` |
| `projects:write` | `create_project`, `create_project_from_template` |
| `projects:stitch` | `render_project` |
| `generate:video` | `generate_video`, `render_shot` |
| `generate:image` | `generate_image` |
| `generate:voice` | `generate_voice` |
| `generate:music` | `generate_music` |
| `storyboards:write` | `plan_storyboard`, `render_storyboard`, `generate_storyboard_*` |
| `brand:read` | `list_brand_agents`, `run_brand_agent`, `generate_creative_bible` |
| `runs:write` | `run_brand_agent` |
| `gates:decide` | `gate_decide` |
| `webhooks:read/write` | `list_webhooks`, `create_webhook`, `delete_webhook` |

---

## Parameter Naming — CRITICAL

The MCP uses **mixed casing**. Check schemas exactly before calling tools.

| Tool | Parameter | Correct form |
|------|-----------|-------------|
| `generate_video` | project association | `project_id` (snake_case) |
| `generate_video` | model | `modelId` (camelCase) |
| `generate_video` | duration | `durationSec` (camelCase) |
| `generate_video` | aspect ratio | `aspectRatio` (camelCase) |
| `render_project` | project | `projectId` (camelCase) |
| `get_project` | project | `projectId` (camelCase) |
| `create_project` | name | `title` (not `name`) |
| `render_shot` | shot | `shot_id` (snake_case) |

**Always call `tools/list` on a new tool and check `inputSchema.properties` for exact key names before writing code.**

---

## Core Workflow: Multi-Shot Video

```
create_project → generate_video (×N) → poll get_job (×N) → render_project
```

### Step 1: Create project
```python
project = call("create_project", {"title": "My Campaign"})
project_id = project["projectId"]
```

### Step 2: Generate each shot (pass project_id)
```python
job_ids = []
for i, shot in enumerate(shots, start=1):
    result = call("generate_video", {
        "project_id": project_id,    # snake_case — critical
        "shot_index": i,
        "modelId": shot["model"],    # camelCase
        "prompt": shot["prompt"],
        "durationSec": shot["duration_sec"],  # camelCase
        "aspectRatio": "9:16",       # camelCase
    })
    job_ids.append(result["jobId"])
```

### Step 3: Poll all jobs until done
```python
def poll_until_done(job_ids, timeout_sec=600):
    deadline = time.time() + timeout_sec
    pending = set(job_ids)
    results = {}
    while pending and time.time() < deadline:
        for job_id in list(pending):
            r = call("get_job", {"jobId": job_id})
            status = r.get("status")
            if status == "succeeded":
                results[job_id] = r["outputUrl"]
                pending.discard(job_id)
            elif status in ("failed", "canceled"):
                print(f"Job {job_id} failed: {r.get('error')}")
                pending.discard(job_id)
        if pending:
            time.sleep(10)
    return results
```

**Status values:** `queued` | `running` | `succeeded` | `failed` | `canceled`  
**Terminal:** `succeeded`, `failed`, `canceled` — stop polling.  
**Non-terminal:** `queued`, `running` — keep polling.

### Step 4: Stitch (only after ALL shots are done)
```python
# All shots must be succeeded before calling this
final = call("render_project", {"projectId": project_id})
video_url = final["outputUrl"]   # direct download URL, permanent
```

> **Important:** `render_project` returns HTTP 400 if any shot hasn't rendered yet.  
> Always confirm all `get_job` calls return `succeeded` before calling `render_project`.

---

## Model Selection

### For Short-Form (Reels, TikTok, Shorts)

| Goal | Model | Price/sec | Notes |
|------|-------|-----------|-------|
| Best overall | `kling-3.0` | $0.10 | Fast (20–35s/shot), cinematic, native audio |
| Best lip-sync / audio | `seedance-4.5` | $0.14 | Phoneme-level sync, supports 4:5 |
| Cheapest stylized | `ws-pika-2.2` | $0.04 | No 1:1, no native audio |
| Cheapest realistic | `wan-2.5` | $0.07 | Great product shots |
| Cinematic 16:9 | `veo-3.1` | $0.15 | Best quality, slow (60–90s/shot) |

### By Aspect Ratio
- **9:16** (TikTok/Reels): `kling-3.0`, `seedance-4.5`, `pika-2.2`
- **4:5** (Instagram): `seedance-4.5` (only cinematic option), `kling-3.0`
- **16:9** (YouTube/Brand film): `veo-3.1`, `kling-3.0`, `wan-2.5`
- **1:1** (Square): `kling-3.0`, `seedance-4.5`, `wan-2.5`

### By Content Type
- **Dialogue / lip-sync**: `veo-3.1` (16:9) or `seedance-4.5` (any)
- **Product demo**: `wan-2.5` or `kling-3.0`
- **Stylized / motion graphics**: `pika-2.2` or `ws-pika-2.2`
- **Brand consistency (multi-shot)**: `kling-3.0` (best shot-to-shot consistency)

Use `suggest_model` to get ranked recommendations:
```python
recs = call("suggest_model", {"shotDescription": "product rotating on pedestal, studio lighting", "kind": "video"})
```

---

## Polling Best Practices

```python
MAX_POLLS = 120        # 120 × 10s = 20 minutes max
POLL_INTERVAL = 10     # seconds

for attempt in range(MAX_POLLS):
    r = call("get_job", {"jobId": job_id})
    if r["status"] in ("succeeded", "failed", "canceled"):
        break
    time.sleep(POLL_INTERVAL)
else:
    raise TimeoutError(f"Job {job_id} did not complete in time")
```

---

## Storyboard Workflow (Director)

For multi-shot campaigns with a brief:

```
plan_storyboard → render_storyboard → render_shot (×N) → poll → render_project
```

```python
# 1. Plan
plan = call("plan_storyboard", {
    "brief": {
        "prompt": "5-shot TikTok ad for running shoes, energetic",
        "aspect_ratio": "9:16",
        "duration_sec": 30,
        "target_shot_count": 5
    },
    "persist": True
})
storyboard_id = plan["storyboard_id"]

# 2. Materialize into shots (idle status)
rendered = call("render_storyboard", {"storyboard_id": storyboard_id})
project_id = rendered["project_id"]
shot_ids = rendered["shot_ids"]

# 3. Render each shot (shots start idle — must call render_shot for each)
job_ids = []
for shot_id in shot_ids:
    r = call("render_shot", {"shot_id": shot_id})
    job_ids.append(r["jobId"])

# 4. Poll + stitch (same as core workflow above)
```

> **Key:** `render_storyboard` creates shots in **idle** status. You must call `render_shot` for each one.

---

## Error Handling

| Error | Meaning | Recovery |
|-------|---------|----------|
| `auth_not_configured` | No BYOK key and no platform key for this vendor | User adds key in Settings, or use a different model |
| `budget_exceeded` | Daily spend cap hit | Wait for reset or increase cap |
| `Model unavailable` | Model is waitlist/stubbed | Call `suggest_model` for alternative |
| `shot(s) haven't rendered yet` | Called `render_project` too early | Poll all jobs to `succeeded` first |
| `User does not have scope: X` | Bearer token missing scope | Regenerate token with required scope |
| `create shot failed` | DB insert failed | Usually a bad `project_id` UUID — verify project exists |

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Passing `"projectId"` to `generate_video` | Use `"project_id"` (snake_case) |
| Passing `"videoUrls"` to `render_project` | Only pass `"projectId"` — shots are already associated |
| Calling `render_project` before all shots done | Poll every `jobId` to `succeeded` first |
| Not passing `project_id` at all | Every call creates a new fragment project |
| Calling `render_storyboard` and expecting shots to render | Shots are `idle` — you must call `render_shot` for each |

---

## Tool Quick Reference

| Tool | Returns | When to use |
|------|---------|-------------|
| `create_project` | `{ projectId, slug }` | Start of every multi-shot run |
| `generate_video` | `{ jobId, projectId }` | Submit each shot |
| `get_job` | `{ kind, status, outputUrl, error }` | Poll until `succeeded`/`failed` |
| `render_project` | `{ ok, outputUrl, durationSec, totalCostCents }` | Stitch after all shots done |
| `get_project` | `{ project, shots[] }` | Inspect project + shot status |
| `list_projects` | `[{ id, name, slug, updated_at }]` | List (max 50) |
| `suggest_model` | ranked model list | Pick best model for a description |
| `plan_storyboard` | `{ storyboard_id, plan, recommended_mode }` | Director planning |
| `render_storyboard` | `{ project_id, shot_ids }` | Materialize plan into idle shots |
| `render_shot` | `{ jobId, modelId, projectId }` | Start rendering an idle shot |
| `generate_image` | `{ imageUrl }` | Synchronous reference image (1–3s) |
| `generate_voice` | `{ audioUrl }` | TTS via ElevenLabs |
| `generate_music` | `{ trackId, audioUrl }` | Background music |
