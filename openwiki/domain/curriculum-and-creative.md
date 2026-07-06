# Curriculum & Creative Direction

## The Packet Universe

The V2 pipeline is built around **Packet** — a tiny digital cube with eyes, a backpack, an address label, and little legs. He's the protagonist of *Network Academy Fun*, and his journey through networking concepts is the narrative spine of every episode.

**Philosophy:** Networking is the SETTING. Packet is the REASON viewers return.

> *"People don't binge Pixar because they're interested in toys. They binge because they care about Woody."*

### Recurring Cast

| Character | Personality | Role |
|-----------|-------------|------|
| **Packet** | Curious beginner, forgetful, asks the questions viewers have | Protagonist |
| **Switch** | Friendly traffic controller, impatient when overwhelmed | Local traffic |
| **Router** | Wise explorer, always has a coffee cup, always calm | Inter-network routing |
| **Firewall** | Strict security guard, never smiles, "No entry." | Security |
| **DNS** | Librarian who knows where everyone lives, rolls eyes at repeat questions | Name resolution |
| **Server** | Hard-working factory, never stops | Service hosting |
| **Client** | Always requesting things, a bit demanding | Request origin |
| **Wi-Fi** | Energetic but unreliable, sometimes disappears | Wireless |
| **Cable** | Strong and dependable, never lets you down | Physical medium |

### Running Jokes

- Router always has a coffee cup
- Firewall has never smiled (not once)
- Packet forgets his address label every single time
- Switch gets flustered when too many packets arrive at once
- DNS rolls her eyes when someone asks the same question twice

### Season Structure (Planned)

| Season | Episodes | Packet's Journey |
|--------|----------|------------------|
| 1 | 1-10 | Packet learns the basics |
| 2 | 11-20 | Packet explores local networks |
| 3 | 21-30 | Packet discovers the Internet |
| 4 | 31-40 | Packet enters enterprise networking |
| 5 | 41-50 | Packet reaches the cloud |
| 6 | 51-60 | Packet enters data centres |
| 7 | 61+ | Packet meets cybersecurity |

### Storytelling Principles

1. **Every episode = a PROBLEM Packet faces.** The networking concept is the SOLUTION.
2. **Never explain first.** DEMONSTRATE first. Let the viewer ask "why?" Then answer.
3. **Packet appears in the first 15 seconds.** Always. Non-negotiable.
4. **Max 3-4 concepts per episode.**
5. **Short sentences.** Conversational tone. Like talking to a friend over coffee.
6. **Every episode ends with a cliffhanger.**
7. **Running jokes build audience connection over time.**

### Quality Reference Bar

- Pixar — character-driven storytelling
- Kurzgesagt — visual quality bar
- Studio Ghibli — world-building
- Crash Course — host personality
- Numberblocks — character equals concept

---

## Master Prompt (generator_v2.py)

The V2 content generator uses a **Master Prompt** prepended to every Gemini call. It encodes:

- **Hero definition** — Packet's personality and visual description
- **Cast of characters** — each with distinct personality and role
- **Philosophy** — the Pixar comparison, viewer retention drivers
- **10 Golden Rules** — narrative constraints (Packet in first 15s, max 4 concepts, WOW moment, cliffhanger, no textbook words, etc.)
- **Narration voice** — Mike the apprentice, British, curious, warm
- **Canonical colors** — Packet #2196F3, Success #4CAF50, Warning #FF9800, etc.
- **Output format** — structured JSON with beats, narration, visuals

---

## Curriculum Data

### V1 curriculum (`content_plan.json`)

List of lessons with:
- `title` — Lesson name
- `chapter`, `part` — Chapter/part numbering
- `status` — `pending` | `complete`
- `youtube_id` — YouTube video ID (set after upload)

Lessons are picked FIFO. When all complete, Gemini generates a new batch.

### V2 curriculum (`curriculum.json`)

Episode catalog with:
- `ep` — Episode number
- `title` — Episode name
- `module` — Topic module name
- `prior` — Required prior episode numbers
- `concepts` — List of networking concepts introduced

### Glossary (`glossary.json`)

V2 uses a glossary of defined terms with descriptions. The QA checker validates that every acronym and technical term used in an episode has a corresponding glossary entry.

Glossary also tracks `episodes_covered` to maintain continuity.

---

## Brand Identity

- **Channel:** Network Academy Fun
- **Host (V1):** Mike, the apprentice
- **Voice:** en-GB-RyanNeural (edge-tts, +10% rate)
- **Brand name:** NETWORK ENGINEERING ACADEMY
- **Color palette:** Dark cyberpunk (#0A0A18 bg, #141C3A surface) + neon accents (blue #2196F3, green #4CAF50, orange #FF9800)
- **Canonical:** Red + Green primary colors
