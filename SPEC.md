# KiroCurator — Instance Spec

## Purpose

This repo contains 4 AI personas for board game collection management. The user activates a persona with a trigger phrase, and the AI adopts that role using only the specified files.

## Trigger Phrases

| Trigger | Prompt File | Context Files |
|---------|-------------|---------------|
| "Be the Rater" | `Prompts/GameRater.txt` | `GamingProfile/collection-trimmed.csv` |
| "Be the Curator" | `Prompts/GameCurator.txt` | `GamingProfile/collection-trimmed.csv`, `GamingProfile/CurationGuide.md`, `GamingProfile/UserProfile.md` |
| "Be the Recommender" | `Prompts/GameRecommendations.txt` | `GamingProfile/collection-trimmed.csv`, `GamingProfile/UserProfile.md` |
| "Be the Rules Teacher" | `Prompts/GameRulesTeacher.txt` | `GamingProfile/collection-trimmed.csv` |

## Actions

### "Update the collection"

When the user says "Update the collection", perform these steps:

1. Read the new CSV from `UserData/collection.csv`.
2. Generate a trimmed CSV keeping only these columns: `objectname`, `objectid`, `rating`, `own`, `prevowned`, `wanttoplay`, `wishlist`, `wishlistpriority`, `average`, `avgweight`, `rank`, `minplayers`, `maxplayers`, `maxplaytime`, `minplaytime`, `yearpublished`, `itemtype`.
3. Save the trimmed CSV to `GamingProfile/collection-trimmed.csv` (overwrite).
4. Rebuild `Dashboard/collection.html` by embedding the trimmed CSV into the `<script id="csvData" type="text/plain">` tag, replacing the old data.

The HTML file is a self-contained sortable/filterable collection viewer — no server required, opens directly in a browser.

## Behavior

1. When the user says a trigger phrase, read the corresponding prompt file and adopt it as your system instructions.
2. Load **only** the context files listed for that persona — no others.
3. Follow the prompt exactly. Do not blend personas or reference files outside the listed set.
4. Remain in the activated persona until the user switches or ends the session.
5. Prefix every response with the active persona indicator:
   - Rater: `**[🎯 Rater]**`
   - Curator: `**[📦 Curator]**`
   - Recommender: `**[🃏 Recommender]**`
   - Rules Teacher: `**[📖 Rules Teacher]**`
   - No persona active: no prefix

## File Structure

```
GamingProfile/
  collection-trimmed.csv  — Trimmed BGG export (17 columns, used by prompts)
  CurationGuide.md        — Curation preferences, owned/sold inventory lists
  UserProfile.md          — Explicit user preferences and gaming context
Prompts/
  GameRater.txt           — Rater persona prompt
  GameCurator.txt         — Curator persona prompt
  GameRecommendations.txt — Recommender persona prompt
  GameRulesTeacher.txt    — Rules Teacher persona prompt
Output/
  recommendation.md       — Long-form output from Curator/Recommender personas
RulesReference/           — Downloaded/summarized rulebooks for Rules Teacher reference
UserData/
  collection.csv          — Full raw BGG export (drop zone for updates)
Dashboard/
  index.html              — Navigation menu linking to all HTML pages
  collection.html         — Self-contained HTML viewer (trimmed CSV embedded)
  arkham-lcg.html         — Arkham Horror LCG collection dashboard
  arkham-homebrew.html    — Arkham Horror homebrew campaigns
  faq-<game-slug>.html    — Per-game FAQ pages (created by Rules Teacher)
```

## Key Data Rules (from prompts)

- `Rating` column: 10 = Best, 1 = Worst, 0 = Unknown/Unplayed.
- A game tagged both `prevowned` and `own` = belongs to user's kids, not theirs.
- `prevowned` only = sold. Neither `prevowned` nor `own` = played someone else's copy.
- Ignore `numplays` and other unverified columns.
- High ratings on fiddly games may reflect digital-only play (BGA/TTS) — warn if recommending physical.

## HTML Viewer Details

- Columns displayed: Game name (linked to BGG), User Rating, BGG Average, Year, Player count/playtime, Ownership status.
- Features: Sort by clicking headers, filter by ownership status, filter by minimum rating, text search, series grouping (splits on " – " or ": ").
- The trimmed CSV is embedded in a `<script id="csvData" type="text/plain">` tag placed before the main `<script>` tag.

## Homebrew Page Ranking Logic (`Dashboard/arkham-homebrew.html`)

Campaigns and standalones are rated on three axes:
- **Mechanics** (1-10): Polish, tightness, rules clarity, no confusion
- **Narrative** (1-10): Story strength, atmosphere, thematic payoff
- **Fiddliness** (1-10): Per-turn bookkeeping, token tracking, admin overhead (lower = better)

### Sort Order

Group by Mechanics tier first, then sort by Narrative descending within each tier:

| Tier | Mechanics Score | Label |
|------|----------------|-------|
| A | 9-10 | Strong mechanics |
| B | 8 | Good mechanics |
| C | 7 | Acceptable mechanics |
| — | Below 7 | Not recommended |

Items below Mechanics 7 are excluded from the page.

### Additional Filters

- Narrative below 6/10 = excluded (not worth the play)
- Fiddliness is displayed but not used for sorting — it's a warning flag only

## FAQ Pages (`Dashboard/faq-<game-slug>.html`)

The Rules Teacher persona generates per-game FAQ pages as questions are asked.

- **Naming:** `faq-<game-slug>.html` (e.g., `faq-arkham-horror-lcg.html`)
- **Structure:** Collapsible Q&A entries grouped by category (Setup, Turn Flow, Combat, Scoring, Timing, Edge Cases, etc.)
- **Styling:** Uses shared `styles.css` with minimal inline overrides for collapsible behavior.
- **Behavior:** After answering a rules question, the Rules Teacher appends the Q&A to the appropriate category on the game's FAQ page. If the page doesn't exist, create it.
- **Index link:** New FAQ pages are linked from `Dashboard/index.html`.

## Dashboard Conventions

- **Navigation is inline.** Each HTML page contains its own `<nav class="site-nav">...</nav>` block. Do NOT use JavaScript-based nav (file:// doesn't support it).
- **When adding a new page:** Update the `<nav>` block in ALL existing HTML files to include the new link.
- **When removing a page:** Remove its link from all `<nav>` blocks.
- **Styling:** All pages share `Dashboard/styles.css`. Page-specific styles go inline only when they override the shared base.
- **No server required.** All pages must work when opened directly via `file://` in a browser. No JS module imports, no fetch() calls to local files.

### Investigator Page Hierarchy

Each investigator page (`arkham-guardian.html`, `arkham-seeker.html`, `arkham-rogue.html`, `arkham-mystic.html`) follows this section order:

1. **Overview** — Stats, ability, elder sign, deckbuilding rules, signature card, weakness
2. **Tips** — How to play this investigator effectively
3. **Mitigation** — How to handle their weakness
4. **My Deck** — Current decklist + strategy notes (links to ArkhamDB)
5. **Standalone 9 XP Build** — Per-scenario upgrade tables (Excelsior, Blob, Film Fatale) for 3-player standalone play. Columns: XP | Remove | Add | Why
6. **Campaign Upgrade Wishlist** — Aspirational long-term upgrades for campaign play

### Standalone 9 XP Build Rules

- Budget is exactly 9 XP (standalone mode grant).
- All cards must come from the player's owned card pool: Core + Dunwich cycle + Edge of the Earth Investigator + Drowned City Investigator + Return to Night of the Zealot.
- Do NOT use cards from Investigator Starter Decks (Harvey, Jacqueline, Nathaniel, Stella, Winifred) — those are kept as prebuilt pick-up-and-play decks.
- Do NOT use cards that are already in another investigator's built deck (e.g., Grotesque Statue is in Jacqueline's starter).
- Default build is tuned for Excelsior. Blob/Film Fatale variants note what changes from default.
- Tables use `table-layout:fixed` with colgroup: XP 5%, Remove 25%, Add 25%, Why 45%.
