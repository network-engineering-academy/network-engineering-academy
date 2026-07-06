# Secrets & YouTube Upload

## Required Environment Variables

| Variable | Purpose | Required For |
|----------|---------|-------------|
| `GOOGLE_API_KEY` | Gemini API for content generation | V1 & V2 |
| `PEXELS_API_KEY` | Background image search (Pexels) | V1 only |
| YouTube OAuth files | See below | Upload |

---

## YouTube Upload System (`src/uploader.py`)

The uploader uses **Google OAuth 2.0** to authenticate with the YouTube Data API v3.

### Files Involved

| File | Purpose | Should Commit? |
|------|---------|---------------|
| `client_secrets.json` | OAuth client ID + secret from Google Cloud Console | ❌ No (secret) |
| `credentials.json` | Refresh + access tokens from user consent | ❌ No (secret) |

### Authentication Flow

```
1. First run (local):
   client_secrets.json ──► OAuth consent screen (browser)
                              │
                              ▼
                         User grants YouTube permission
                              │
                              ▼
                         credentials.json saved (refresh + access tokens)

2. Subsequent runs (local or CI):
   credentials.json ──► Token still valid? ──► Use it
                              │
                              ▼ No
                         Refresh token valid? ──► Auto-refresh
                              │
                              ▼ No
                         Re-run OAuth flow (local browser)
```

### Setting Up for the First Time

1. **Get `client_secrets.json`:**
   - Go to [Google Cloud Console](https://console.cloud.google.com/)
   - Enable **YouTube Data API v3**
   - Create OAuth 2.0 credentials (Desktop application type)
   - Download → save as `client_secrets.json` in repo root

2. **Run locally to generate `credentials.json`:**
   ```bash
   python -c "from src.uploader import get_authenticated_service; get_authenticated_service()"
   ```
   This opens a browser — sign in with the YouTube channel's Google account and grant permission.

3. **Verify:**
   ```bash
   python src/uploader.py  # Test authentication
   ```

---

## CI/CD Credential Setup

For GitHub Actions to upload, credentials must be base64-encoded as Secrets:

```bash
base64 -w0 client_secrets.json > client_secrets.b64
base64 -w0 credentials.json > credentials.b64
```

Then add to GitHub repo Settings → Secrets and variables → Actions:
- `CLIENT_SECRET_B64` — contents of `client_secrets.b64`
- `CREDENTIALS_B64` — contents of `credentials.b64`

### When Credentials Expire

YouTube refresh tokens expire if unused for **6 months**. When this happens:

1. Run locally: `python main.py` — it will prompt for re-auth
2. Fresh `credentials.json` will be saved
3. Re-encode and update GitHub Secrets

---

## V1 Content Pipeline (`src/generator.py`)

The V1 generator uses:
- **Gemini 2.5 Flash** via `google-genai` SDK (key from `GOOGLE_API_KEY`)
- **Pexels API** for background images (key from `PEXELS_API_KEY`)
- **edge-tts** for narration (no key needed — uses Microsoft's free edge TTS)

### API Key Locations

```
# For local development: ~/.hermes/.env or export before running
export GOOGLE_API_KEY=your_key_here
export PEXELS_API_KEY=your_key_here

# For CI: GitHub Secrets (see daily-pipeline.md)
```

---

## V2 Content Pipeline (`src/generator_v2.py`)

Same Gemini API key (`GOOGLE_API_KEY`) — uses the `google-genai` SDK.

No Pexels dependency — V2 generates all visuals procedurally via PIL and cairo.

---

## Security Notes

- **Never commit** `client_secrets.json`, `credentials.json`, or `.env` to git
- `.gitignore` should already exclude these
- The GitHub Actions workflow decodes secrets at runtime only — they never appear in logs
- YouTube OAuth tokens grant **full channel access** — treat them as sensitive
