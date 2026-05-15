# Platform Keys Setup

Platform keys let Varosity charge API costs to its own provider accounts and debit users via Varosity Credits. Without a platform key for a vendor, that vendor is BYOK-only.

The env var pattern is `{VENDOR_UPPERCASE}_PLATFORM_KEY` — e.g. `FAL_PLATFORM_KEY`.

---

## Status

| Vendor | Env Var | Status |
|--------|---------|--------|
| Fal | `FAL_PLATFORM_KEY` | ✅ Done |
| ElevenLabs | `ELEVENLABS_PLATFORM_KEY` | ❌ Error |
| MuAPI | `MUAPI_PLATFORM_KEY` | ❌ Error |
| WaveSpeed | `WAVESPEED_PLATFORM_KEY` | ✅ Done |
| Runway | `RUNWAY_PLATFORM_KEY` | ⏭ Skip (no account — BYOK only) |
| OpenAI | `OPENAI_PLATFORM_KEY` | ⬜ Pending |
| Replicate | `REPLICATE_PLATFORM_KEY` | ⬜ Pending |
| Google | `GOOGLE_PLATFORM_KEY` | ⬜ Pending |
| Fish Audio | `FISH_AUDIO_PLATFORM_KEY` | ⬜ Pending |
| Cartesia | `CARTESIA_PLATFORM_KEY` | ⬜ Pending |
| Luma | `LUMA_PLATFORM_KEY` | ⬜ Pending |
| Pika | `PIKA_PLATFORM_KEY` | ⬜ Pending |
| Hailuo | `HAILUO_PLATFORM_KEY` | ⬜ Pending |

---

## Process (repeat for each pending vendor)

### 1. Create account & add payment method
Sign up or log in at the provider's developer portal (links below), then add a credit card so API charges bill to Varosity.

### 2. Generate an API key
Go to the API / developer settings page and create a new key. Label it `varosity-platform` so it's identifiable.

### 3. Add to Vercel
```bash
vercel env add <ENV_VAR_NAME> production
# paste the key when prompted
```

### 4. Mark done above
Update the Status table in this file.

### 5. Redeploy after all keys are added
```bash
vercel deploy --prod
```
One redeploy at the end is fine — no need to deploy after each key.

---

## Provider Sign-up URLs

| Vendor | URL |
|--------|-----|
| OpenAI | https://platform.openai.com/api-keys |
| Replicate | https://replicate.com/account/api-tokens |
| Google | https://aistudio.google.com/apikey |
| Fish Audio | https://fish.audio/go-api/ |
| Cartesia | https://play.cartesia.ai/keys |
| Luma | https://lumalabs.ai/dream-machine/api/keys |
| Pika | https://pika.art/account |
| Hailuo | https://hailuoai.video/api |

---

## Activating credits billing per vendor

Once platform keys are in Vercel, go to **Varosity Settings → Providers** and toggle each vendor from **BYOK** to **Platform** mode. Credits will be debited automatically on the first successful render for that vendor.
