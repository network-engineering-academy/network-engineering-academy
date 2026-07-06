# Architecture Overview

## Two Pipeline Generations

This repo contains **two independent implementations** that serve the same YouTube channel:

### V1 — Slide-based (Production)

**Entrypoint:** `main.py`  
**Source:** `src/generator.py` + `src/uploader.py`  
**Status:** Runs daily via GitHub Actions

V1 generates slide-based educational videos hosted by **Mike, the apprentice** persona with en-GB-RyanNeural voice. Each lesson produces:
- A long-form video (1920×1080, ~4 min)
- A YouTube Short (1080×1920, ~60s)
- Custom thumbnail for each format
- Background music overlay

### V2 — Animated "Packet Universe" (In Development)

**Entrypoint:** `main_v2.py`  
**Source:** `src/generator_v2.py`, `src/animator.py`, `src/frame_animator.py`, `src/qa_checker.py`  
**Status:** Local-only, no CI trigger

V2 produces animated episodes in the **Packet Universe** — a character-driven networking series styled after Kurzgesagt. Each episode features:
- Frame-by-frame 24fps animations
- Progressive diagram reveals (per-beat)
- Character-driven storytelling (Packet, Router, Switch, Firewall, DNS…)
- SFX synthesis
- QA gate (7-item checklist before publishing)

---

## V1 Pipeline Stages

```
content_plan.json ──► Gemini 2.5 Flash ──► Lesson Script
                        │
                        ▼
                   edge-tts (RyanNeural) ──► Audio clips (per slide)
                        │
                        ▼
                   Pexels API ──► Background images
                        │
                        ▼
                   PIL slide rendering ──► Slide images
                        │
                        ▼
                   MoviePy composition ──► Long video + Short + Thumbnail
                        │
                        ▼
                   YouTube Data API v3 ──► Upload both formats
                        │
                        ▼
                   content_plan.json updated ──► git commit
```

### Detailed Stages

1. **Curriculum Load** — Reads `content_plan.json`, picks next `pending` lesson
2. **Content Generation** — Gemini 2.5 Flash writes 7-8 slide lesson + short highlight
3. **TTS** — `edge-tts` with `en-GB-RyanNeural` voice at +10% rate, converts to WAV
4. **Visuals** — Fetches Pexels background images or falls back to solid color
5. **Slide Rendering** — PIL compositing with Mike avatar, icons, bullet points
6. **Video Assembly** — MoviePy concatenates slides + audio + background music
7. **Thumbnail** — Title overlay on dark background with brand colors
8. **Upload** — YouTube Data API v3 with OAuth 2.0, both long-form and Short
9. **Tracking** — Marks lesson `complete` with YouTube ID, commits back

---

## V2 Pipeline Stages

```
curriculum.json ──► generator_v2.py (Master Prompt + Story Architect)
                        │
                        ▼
                   QA Checker (7-item checklist)
                        │
                        ▼
                   edge-tts (RyanNeural) ──► Audio per beat
                        │
                        ▼
                   frame_animator.py ──► Frame-by-frame 24fps animations
                        │
                        ▼
                   animator.py ──► Composite with intro/outro
                        │
                        ▼
                   Video file + Thumbnail
```

### V2 QA Checklist (7 items)

1. **Hook present** — first 8 sec states the outcome (≥5 words)
2. **No undefined terms** — all acronyms checked against glossary
3. **Visual beat change** — every 10-15 sec (≥6 beats)
4. **Packet appears** — within first 15 seconds
5. **Max 4 new concepts** — vocabulary count check
6. **Cliffhanger or WOW moment** — episode-ending hook
7. **Narration-to-screen alignment** — no text-on-screen violations

---

## Code Map

```
Root
├── main.py                    V1 pipeline orchestrator
├── main_v2.py                 V2 pipeline orchestrator
├── CREATIVE_BRIEF.py          Canonical creative direction (Packet Universe)
├── content_plan.json          V1 lesson queue with status
├── curriculum.json            V2 episode catalog with prior mapping
├── glossary.json              V2 defined terms
│
├── src/
│   ├── __init__.py            Empty (package marker)
│   ├── generator.py           V1: Gemini content gen, slide render, TTS, avatar
│   ├── generator_v2.py        V2: Master Prompt + Story Architect → JSON
│   ├── animator.py            V2: Frame compositing engine (Kurzgesagt-style)
│   ├── frame_animator.py      V2: Per-beat 24fps frame generation with easing
│   ├── qa_checker.py          V2: 7-item quality gate
│   ├── uploader.py            YouTube Data API v3 upload (shared by V1 & V2)
│   ├── icons.py               28×28 geometric icon library for slide bullets
│   ├── illustrations.py       Anti-aliased device illustrations via cairo
│   └── diagrams.py            Topic-specific technical diagrams (progressive)
│
├── scripts/
│   └── create_avatar.py       Generate Mike the apprentice avatar assets
│
├── assets/
│   ├── fonts/                 Inter-Regular, Inter-SemiBold, Arial variants
│   ├── music/bg_music.mp3     Background music loop
│   └── characters/            Mike avatar PNGs (silent, talking, semi-talking)
│
├── output/                    Generated videos, audio, slides, thumbnails
│
├── .github/workflows/
│   └── main.yml               Daily 7 AM UTC scheduled pipeline
│
├── client_secrets.json        Google OAuth client secrets (DO NOT COMMIT)
├── credentials.json           YouTube OAuth tokens (auto-refreshed)
├── requirements.txt           Python dependencies
└── README.md                  Project README
```

---

## Data Flow

### V1
```
content_plan.json ──► Lesson picked ──► AI generates content
    ▲                                          │
    │                                          ▼
    └── Mark complete ◄── Upload to YT ◄── Render video
```

### V2
```
curriculum.json ──► Episode picked ──► Story beats generated
    ▲                                          │
    │                                          ▼
    └── Update glossary ◄── QA check ◄── Animate frames
                                    │
                                    ▼
                              Upload to YT
```

---

## Dependencies

| Package | Use |
|---------|-----|
| `google-genai` | Gemini API for content generation |
| `moviepy==1.0.3` | Video composition and rendering |
| `Pillow==9.5.0` | Image processing, slide creation |
| `edge-tts` | Microsoft edge TTS (RyanNeural voice) |
| `pydub` | Audio format conversion (MP3→WAV) |
| `requests` | Pexels API calls |
| `google-api-python-client` | YouTube Data API v3 |
| `google-auth-oauthlib` | OAuth 2.0 for YouTube |
| `gTTS` | Fallback TTS (V1 only) |
| `cairocffi` | Anti-aliased 2D rendering (V2, illustrations.py) |

## Design Decisions

- **Two separate pipelines** — V1 is production-stable slide-based; V2 explores character-driven animation without risking daily uploads
- **Gemini 2.5 Flash** for content generation — fast, cost-effective, creative
- **edge-tts (RyanNeural)** for narration — free, high-quality British male voice
- **No database** — everything is file-based (JSON + local assets), making the GitHub Actions runner stateless
- **QA gate in V2** — prevents publishing episodes that violate spec before they reach YouTube
