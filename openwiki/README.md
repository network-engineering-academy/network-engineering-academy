# Network Engineering Academy — Wiki

> Autonomous YouTube video production pipeline for network engineering education.
> Two parallel implementations: V1 (slide-based, production) and V2 (animated "Packet Universe", in-development).

## Quick Start

```bash
# V1 — slide-based lesson (Mike the apprentice)
python main.py

# V2 — animated Packet Universe episode
python main_v2.py
```

Both pipelines read from a curriculum file, generate lesson content via Gemini, produce audio (edge-tts), render video, and upload to YouTube via the Data API v3.

---

## Wiki Contents

| Page | What it covers |
|------|----------------|
| [Architecture Overview](architecture/overview.md) | V1 vs V2 comparison, pipeline stages, code map |
| [Daily Pipeline](workflows/daily-pipeline.md) | GitHub Actions cron flow, env vars, secrets, commit-back |
| [Curriculum & Creative](domain/curriculum-and-creative.md) | Packet Universe, Master Prompt, creative brief, glossary |
| [Secrets & Upload](operations/secrets-and-upload.md) | YouTube OAuth, required secrets, local vs CI setup |

---

## Project Identity

- **YouTube Channel:** Network Academy Fun
- **Host (V1):** Mike, the apprentice
- **Voice:** en-GB-RyanNeural (edge-tts, +10% rate)
- **Brand:** Network Engineering Academy
- **Brand Colors:** Red + Green primary, dark cyberpunk/neon glow aesthetic

---

## Key Files

| File | Role |
|------|------|
| `main.py` | V1 pipeline entrypoint (production) |
| `main_v2.py` | V2 pipeline entrypoint (in-development) |
| `CREATIVE_BRIEF.py` | Canonical creative direction for Packet Universe |
| `content_plan.json` | V1 curriculum: lesson queue with status tracking |
| `curriculum.json` | V2 episode catalog with prior knowledge mapping |
| `glossary.json` | V2 defined terms (used by QA checker) |
| `src/generator.py` | V1: Gemini content gen, slide rendering, TTS, avatar |
| `src/generator_v2.py` | V2: Master Prompt + Story Architect → storyboard JSON |
| `src/animator.py` | V2: Kurzgesagt-inspired frame composition |
| `src/frame_animator.py` | V2: frame-by-frame animation engine (24fps) |
| `src/qa_checker.py` | V2: 7-item spec checklist before publishing |
| `src/uploader.py` | YouTube Data API v3 upload with OAuth flow |
| `src/icons.py` | 28×28 geometric icon library for slide bullets |
| `src/diagrams.py` | Topic-accurate technical illustrations (progressive reveal) |
| `src/illustrations.py` | Anti-aliased network device illustrations via cairo |
| `scripts/create_avatar.py` | Generate Mike the apprentice avatar PNGs |
| `.github/workflows/main.yml` | Daily 7 AM UTC GitHub Actions schedule |

---

## AGENTS.md Integration

This wiki lives under `openwiki/`. When working on this repo:

- Start here for project structure and goals
- Check `architecture/overview.md` for V1 vs V2 differences
- Check `workflows/daily-pipeline.md` for CI/CD details
- Check `domain/curriculum-and-creative.md` for content direction
- Check `operations/secrets-and-upload.md` before running locally
