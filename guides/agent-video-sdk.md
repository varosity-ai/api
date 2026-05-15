---
title: "Varosity Agent Video SDK"
description: "Production-grade video generation for any agent. One API key, all platforms, no external dependencies."
surface: api
triggers: [agent, sdk, video, kling, veo, runway, python, hermes, orchestrate, batch]
difficulty: intermediate
estimatedMinutes: 10
---

# Varosity Agent Video SDK

**Production-grade video generation for any agent.**

Design philosophy: **Agent-first, platform-agnostic, dependency-free.**

Any agent can integrate this SDK and start generating cinema-quality videos in minutes — no external workflow required.

---

## Core Promise

```
Any Agent + Varosity API Key + This Skill = Production Video
```

Generate professional videos for:
- Marketing campaigns
- Real estate property tours
- E-commerce product showcases
- Educational content
- Finance/startup pitches
- Travel content
- Social media (Instagram, TikTok, YouTube)
- Custom branding

---

## What This SDK Does

### 1. Unified Platform Access

```
Single Varosity API Key → All Platforms
├── Video: Kling 3.0, Veo 3.1, Runway, Seedance
├── Music: Suno AI
├── Voice: ElevenLabs
├── Rendering: Varosity Composite Engine
└── Billing: Single Credits pool
```

### 2. Splash Frame Consistency

```
Generate reference image (visual bible)
        ↓
All video shots inherit same aesthetic
        ↓
Result: Visually cohesive production
```

### 3. Intelligent Compositing

```
Multiple shots (parallel generation)
        ↓
Audio sync (music + voice-over)
        ↓
Auto-transitions & effects
        ↓
Single production-ready MP4
```

### 4. Optional Branding

```
YOUR branding: Logo, colors, tagline, location
        OR
No branding: Plain professional output
        OR
Custom: Inject anything you want
```

---

## Universal Architecture

The SDK is **decomposed into modules** so agents can use what they need:

```python
from varosity_sdk import VarosityAgent

# Minimal usage
agent = VarosityAgent(api_token="vsk_...")
video = agent.generate_simple_video(
    concept="Product showcase",
    duration_sec=30
)

# OR full orchestration
video = agent.orchestrate_production(
    project_title="Real Estate Tour",
    shots=[...],
    audio={...},
    branding={...},
    end_card={...}
)

# OR just parts
splash = agent.generate_splash_frame(mood="luxury")
shots = agent.generate_video_shots(descriptions=[...])
music = agent.generate_music(style="ambient")
# ... compose yourself
```

**Agents choose their level of complexity.**

---

## Use Case Examples

### Marketing Agency Agent

```python
for product in products:
    video = agent.generate_product_video(
        product_name=product["name"],
        product_description=product["description"],
        images=[...],
        branding={
            "logo": "agency_logo.png",
            "colors": ["#FF6B35", "#004E89"],
            "tagline": "Crafted for You"
        }
    )

# Result: Production-ready MP4s for Instagram/TikTok
```

### Real Estate Agent

```python
video = agent.generate_property_tour(
    property_address="123 Ocean View Drive",
    property_images=[exterior, living_room, kitchen, bedrooms],
    highlights=["Ocean views", "Chef's kitchen", "Smart home", "Pool & spa"],
    price="$2.5M",
    branding={
        "agent_name": "Sarah Chen",
        "brokerage": "Luxury Homes Co.",
        "contact": "sarah@luxuryhomes.com"
    }
)

# Result: Ready for MLS listing, Instagram Stories, YouTube Shorts
```

### E-Commerce Agent

```python
for sku in inventory:
    video = agent.generate_product_showcase(
        product=sku,
        angles=["front", "side", "detail", "in-use"],
        music_style="upbeat_modern",
        end_card={"call_to_action": "Shop Now", "link": sku.product_url}
    )

# Result: Video catalog ready for website, ads, social media
```

### Finance / Startup Agent

```python
video = agent.generate_pitch_video(
    company_name="TechStartup",
    problem_statement="Healthcare data fragmentation",
    solution="AI-powered unification platform",
    slides=[...],
    narration="Our platform solves...",
    branding={"colors": ["#0A1428", "#06A564"]}
)

# Result: 2-minute explainer ready for investor meetings
```

### Educational Content Agent

```python
video = agent.generate_educational_video(
    topic="Python Web Development",
    sections=[
        {"title": "Setup", "duration": 3},
        {"title": "Basic Concepts", "duration": 5},
        {"title": "Live Demo", "duration": 8},
        {"title": "Best Practices", "duration": 4}
    ],
    code_snippets=[...],
    course_branding={"logo": "academy_logo.png", "watermark": "CoursePlatform.com"}
)

# Result: Professional course video ready for YouTube/Udemy
```

### Travel Content Agent

```python
video = agent.generate_travel_montage(
    destination="Iceland",
    highlights=["Waterfalls", "Northern Lights", "Local Culture"],
    travel_photos=[...],
    music={"style": "cinematic_adventure", "mood": "inspiring"},
    branding={"travel_blog_name": "World Wanderer", "social_handles": "@wanderer"}
)

# Result: TikTok/Instagram Reels/YouTube Shorts ready
```

---

## API Reference

### Basic Generation

```python
class VarosityAgent:

    def generate_simple_video(
        self,
        concept: str,
        duration_sec: int = 30,
        style: str = "cinematic"
    ) -> ProductionResult:
        """
        Simplest interface. AI figures out everything.

        Args:
            concept: "Product showcase", "Property tour", etc.
            duration_sec: Target video length
            style: "cinematic", "professional", "casual", "energetic"

        Returns:
            ProductionResult with video_url, local_path, specs
        """

    def generate_video_shots(
        self,
        descriptions: List[str],
        platform_selection: str = "auto"
    ) -> List[str]:
        """Generate individual shots without orchestration."""

    def generate_music(
        self,
        style: str,
        duration_sec: int,
        mood: str = "neutral"
    ) -> str:
        """Generate original music via Suno."""

    def generate_voice(
        self,
        text: str,
        voice_id: str = "default",
        pace: str = "natural"
    ) -> str:
        """Generate narration via ElevenLabs."""

    def orchestrate_production(
        self,
        project_title: str,
        shots: List[Dict],
        audio: Dict = None,
        branding: Dict = None,
        end_card: Dict = None
    ) -> ProductionResult:
        """Full 8-stage pipeline."""
```

### Branding (Optional)

```python
branding_none = None  # Plain professional video

branding_simple = {
    "logo": "my_logo.png",
    "tagline": "Your tagline here"
}

branding_full = {
    "logo": "logo.png",
    "tagline": "Your tagline",
    "colors": {
        "primary": "#FF6B35",
        "secondary": "#004E89",
        "accent": "#FFFFFF"
    },
    "location_text": "Optional city/website",
    "style": "modern"  # or "classic", "minimal", "premium"
}
```

### No External Dependencies

```python
# Just generate and download — no third-party integrations required
result = agent.orchestrate_production(
    project_title="My Video",
    shots=[...]
)
print(f"Video saved to: {result.local_path}")
# Agent does whatever it wants with the file
```

---

## Integration Guide

Works with **any agent** — Hermes, OpenClaw, LangChain, CrewAI, AutoGen, n8n, custom Python scripts, or anything else that can run Python and make HTTP requests.

### Step 1: Install

**Any agent / generic:**
```bash
pip install varosity-sdk
# or copy varosity_sdk.py directly into your agent's working directory
```

**Hermes agents:**
```bash
hermes skills install varosity-agent-video-sdk
# or manually place in ~/.hermes/skills/mlops/
```

**OpenClaw / other agent frameworks:** drop `varosity_sdk.py` into your skills/tools directory and import it like any other module.

### Step 2: Get Your API Key

Sign up at [varosity.ai](https://varosity.ai), generate an API key (`vsk_…`) from `/app/keys`, then:

```bash
export VAROSITY_API_KEY="vsk_..."
```

### Step 3: Use in Your Agent

```python
from varosity_sdk import VarosityAgent
import os

def generate_content(self, request):
    varosity = VarosityAgent(api_token=os.getenv("VAROSITY_API_KEY"))

    video = varosity.orchestrate_production(
        project_title=request["title"],
        shots=request["shots"],
        audio=request.get("audio"),
        branding=request.get("branding")
    )

    return {
        "status": "success",
        "video_url": video.final_url,
        "local_path": video.local_path,
        "duration": video.duration_sec
    }
```

### Step 4: Scale to Any Use Case

```python
# Social media agent
video = varosity.generate_simple_video(concept="Daily motivation quote", style="inspirational")

# Real estate agent
video = varosity.generate_property_tour(address="123 Main St", images=[...])

# E-commerce agent
video = varosity.generate_product_showcase(product=sku, angles=["front", "side", "in-use"])

# Finance agent
video = varosity.generate_pitch_video(company=startup, slides=[...])
```

---

## Competitive Positioning

| Feature | OpenArt Smart Shot | Varosity Agent SDK |
|---------|-------------------|--------------------|
| **Setup** | Web UI required | Any agent (Hermes, OpenClaw, LangChain, etc.) |
| **Audio** | None | Music + voice built-in |
| **Branding** | Not supported | Full customization or none |
| **Control** | Low (automated) | High (modular) |
| **Platforms** | Single (Seedance) | Kling · Veo · Runway · Seedance |
| **Composability** | No (monolithic) | Yes (use any part) |
| **Universal** | Web platform only | Any agent can use |
| **Use Cases** | Video generation | Any workflow |

---

## Roadmap

```
v2.1  Template library (marketing, real estate, e-commerce, finance)
v2.2  Local caching — splash frame cache, credit optimization
v2.3  Batch processing — parallel jobs, queue management, cost analytics
v2.4  Real-time monitoring — job status API, credit tracking dashboard
```

---

## Files Structure

```
~/.hermes/skills/mlops/varosity-agent-video-sdk/
├── SKILL.md                     (this file)
├── scripts/
│   ├── varosity_sdk.py          (main SDK)
│   ├── orchestrator.py          (8-stage pipeline)
│   ├── platform_selection.py    (smart model routing)
│   └── branding_engine.py       (optional branding)
├── references/
│   ├── api-reference.md
│   ├── use-cases.md
│   ├── troubleshooting.md
│   └── best-practices.md
├── templates/
│   ├── simple-video.json
│   ├── product-showcase.json
│   ├── property-tour.json
│   ├── pitch-video.json
│   ├── educational.json
│   ├── travel-montage.json
│   └── custom-branding.json
└── examples/
    ├── marketing_agent.py
    ├── real_estate_agent.py
    ├── ecommerce_agent.py
    ├── finance_agent.py
    ├── education_agent.py
    └── social_media_agent.py
```

---

**One API key. All platforms. Any agent. Pure video production.**

✅ Marketing agents — campaign videos  
✅ Real estate agents — property tours  
✅ E-commerce agents — product showcases  
✅ Finance agents — pitch animations  
✅ Social media agents — TikTok/Reels  
✅ Education agents — course tutorials  
✅ Travel agents — destination montages  
✅ Custom agents — whatever they need  
