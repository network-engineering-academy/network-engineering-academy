# AGENTS.md — Network Engineering Academy

## Wiki Available

This repository has a maintained wiki at `openwiki/`. Before making changes or answering questions about this codebase, check the wiki first:

- `openwiki/README.md` — Index and navigation
- `openwiki/architecture/overview.md` — V1 vs V2, pipeline stages, code map
- `openwiki/workflows/daily-pipeline.md` — GitHub Actions CI/CD
- `openwiki/domain/curriculum-and-creative.md` — Creative direction, Packet Universe
- `openwiki/operations/secrets-and-upload.md` — YouTube OAuth, env vars

## Convention

- `main.py` is the production pipeline (V1, slide-based)
- `main_v2.py` is the in-development animated pipeline (V2, Packet Universe)
- GitHub Actions runs `main.py` daily at 7:00 AM UTC
- `content_plan.json` tracks V1 lesson status
- `curriculum.json` + `glossary.json` drive V2 episode generation

## Quality Expectations

- V1: Mike the apprentice host, en-GB-RyanNeural voice, slide-based
- V2: Kurzgesagt-quality animation, character-driven, QA-gated
- Zero narration text on screen (V2)
- Packet appears in first 15 seconds (V2)
