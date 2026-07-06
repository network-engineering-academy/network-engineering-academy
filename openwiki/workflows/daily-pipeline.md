# Daily Pipeline — GitHub Actions

## Schedule

The V1 pipeline runs daily at **7:00 AM UTC** (12:30 PM IST) via:

```yaml
schedule:
  - cron: '0 7 * * *'
```

Also supports manual trigger via `workflow_dispatch` on the GitHub Actions tab.

## Workflow: `main.yml`

### Steps

1. **Checkout** — `actions/checkout@v4`
2. **Python setup** — `actions/setup-python@v5` with Python 3.11 + pip cache
3. **System deps** — Installs ImageMagick, ffmpeg; patches ImageMagick `policy.xml` to allow 16384×16384 pixel operations
4. **Python deps** — `pip install -r requirements.txt`
5. **Restore credentials** — Decodes `CLIENT_SECRET_B64` → `client_secrets.json` and `CREDENTIALS_B64` → `credentials.json` from GitHub Secrets
6. **Run pipeline** — `python main.py` with `GOOGLE_API_KEY` and `PEXELS_API_KEY` env vars
7. **Upload artifacts** — Always uploads audio (`.wav`) and video (`.mp4`) files for debugging
8. **Generate demo GIF** — Converts first 12 seconds of latest video to `images/demo.gif` at 320px wide
9. **Commit and push** — Updates `content_plan.json` and `images/demo.gif` back to repo

### Required Secrets

| Secret | Purpose | Source |
|--------|---------|--------|
| `GOOGLE_API_KEY` | Gemini API for content generation | Google AI Studio |
| `PEXELS_API_KEY` | Background image search | Pexels API |
| `CLIENT_SECRET_B64` | YouTube OAuth client secrets (base64) | Google Cloud Console |
| `CREDENTIALS_B64` | YouTube OAuth tokens (base64, auto-refreshed) | First local auth run |

### Credential Flow

1. **First time (local):** `client_secrets.json` is obtained from Google Cloud Console. Run `uploader.py` locally — it opens a browser for OAuth consent, saves `credentials.json`
2. **Encode for CI:** Both files are base64-encoded and stored as GitHub Secrets
3. **Daily run:** Workflow decodes both files, runs pipeline. YouTube token auto-refreshes via `google-auth` if expired
4. **Token refresh:** If `credentials.json` expires entirely, re-auth locally and re-encode

## Git Commit Behavior

After a successful run, the workflow commits:
- `content_plan.json` — updated lesson status + YouTube IDs
- `images/demo.gif` — fresh preview GIF

Commit message: `feat: Update content plan and demo GIF after daily video production`

The commit uses `github-actions[bot]` as the author.

## Debugging

Failed or partial runs upload artifacts regardless of outcome:
- `audio-debug-files` — all `.wav` files from `output/`
- `final-videos` — all `.mp4` files from `output/`

Download these from the Actions run page to inspect what was produced.

## Local vs CI Differences

| Aspect | GitHub Actions | Local |
|--------|---------------|-------|
| OS | ubuntu-latest | Any |
| Credentials | From Secrets (base64) | `client_secrets.json` + `credentials.json` on disk |
| Gemini key | `GOOGLE_API_KEY` secret | `GOOGLE_API_KEY` in env or `.env` |
| Pexels key | `PEXELS_API_KEY` secret | `PEXELS_API_KEY` in env |
| Output artifacts | Uploaded to run page | Written to `output/` locally |
| Git push | Automatic on success | Manual |
| ImageMagick | Installed + patched via apt | Must be installed manually |
