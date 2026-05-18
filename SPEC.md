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
  recommendation.md       — (Deprecated) All recommendations now go directly into Dashboard HTML pages
RulesReference/           — Downloaded/summarized rulebooks for Rules Teacher reference
UserData/
  collection.csv          — Full raw BGG export (drop zone for updates)
Dashboard/
  index.html              — Redirect to collection.html
  collection.html         — Self-contained HTML viewer (trimmed CSV embedded, home page)
  kickstarter.html        — Kickstarter watchlist
  narrative-finder.html   — Narrative game finder
  styles.css              — Shared styles
  games.js                — Collection data for collection.html
  kickstarters.js         — Kickstarter watchlist data
  arkham/
    lcg.html              — Arkham Horror LCG collection dashboard
    official.html         — Official campaigns ranked
    homebrew.html         — Homebrew campaigns ranked
    ranked.html           — Combined rankings
    faq.html              — Rules FAQ (per-game clarifications)
  investigators/
    index.html            — Investigator landing page (overview + beginner picks)
    guardian.html          — Zoey/Roland shared deck + tips
    seeker.html           — Rex deck + tips
    rogue.html            — Jenny deck + tips
    mystic.html           — Agnes deck + tips
    survivor.html         — Pete deck + tips
    lily.html             — Lily Chen page (Sleepy Hollow / Blob Team 2)
    starters.html         — 5 starter deck tips + standalone upgrades
  scenarios/
    index.html            — Scenario landing page (investigator recommendations table)
    excelsior.html        — Murder at the Excelsior Hotel guide
    blob.html             — The Blob That Ate Everything guide
    film-fatale.html      — Film Fatale guide
    wendigo.html          — Against the Wendigo guide (homebrew)
    consternation.html    — Consternation on the Constellation guide (homebrew)
    sleepy-hollow.html    — The Legend of Sleepy Hollow guide (homebrew)
  campaigns/
    index.html            — Campaign guides landing page (team composition tables)
    zealot.html           — Night of the Zealot campaign guide
    dunwich.html          — The Dunwich Legacy campaign guide
    carcosa.html          — The Path to Carcosa campaign guide
    forgotten-age.html    — The Forgotten Age campaign guide
    edge-of-earth.html    — Edge of the Earth campaign guide
    drowned-city.html     — The Drowned City campaign guide
    homebrew.html         — Homebrew campaign guides (Dark Matter, Cyclopean, Alice)
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

## Homebrew Page Ranking Logic (`Dashboard/arkham/homebrew.html`)

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

## FAQ Page (`Dashboard/arkham/faq.html`)

The Rules Teacher persona appends Q&A entries to the FAQ page as questions are asked.

- **Location:** `Dashboard/arkham/faq.html` (single page for all Arkham Horror LCG rules questions)
- **Structure:** Collapsible Q&A entries grouped by category (General, Investigation, Skill Tests, Enemies & Combat, Mythos & Doom, Timing & Keywords, Campaign & Deckbuilding, Edge Cases)
- **Styling:** Uses shared `styles.css`. FAQ-specific styles use `.faq-entry`, `.faq-question`, `.faq-answer` classes (not `.card`/`.card-title` to avoid collision with shared styles).
- **Behavior:** After answering a rules question, the Rules Teacher appends the Q&A to the appropriate category. New categories can be added as needed.

## Dashboard Conventions

- **Navigation is inline.** Each HTML page contains its own `<nav class="site-nav">...</nav>` block. Do NOT use JavaScript-based nav (file:// doesn't support it).
- **When adding a new page:** Update the `<nav>` block in ALL existing HTML files to include the new link.
- **When removing a page:** Remove its link from all `<nav>` blocks.
- **Styling:** All pages share `Dashboard/styles.css`. Page-specific styles go inline only when they override the shared base.
- **No server required.** All pages must work when opened directly via `file://` in a browser. No JS module imports, no fetch() calls to local files.

### Investigator Page Hierarchy

#### Class Emoji Mapping

| Class | Emoji | Color |
|-------|-------|-------|
| Guardian | 🛡️ | Blue (#1e40af) |
| Seeker | 🔍 | Gold (#854d0e) |
| Rogue | 💵 | Green (#166534) |
| Mystic | 🔮 | Purple (#6b21a8) |
| Survivor | ⭐ | Red (#991b1b) |
| Starters | 📘 | — |

Use these consistently in h1 headings, class badges, and card icons.

Each investigator page (`investigators/guardian.html`, `investigators/seeker.html`, `investigators/rogue.html`, `investigators/mystic.html`) follows this section order:

1. **Overview** — Stats, ability, elder sign, deckbuilding rules, signature card, weakness
2. **Tips** — How to play this investigator effectively
3. **Mitigation** — How to handle their weakness
4. **My Deck** — Current decklist + strategy notes (links to ArkhamDB)
5. **Standalone 9 XP Build** — Per-scenario upgrade tables (Excelsior, Blob, Film Fatale) for 3-player standalone play. Columns: XP | Remove | Add | Why
6. **Campaign Upgrade Wishlist** — Aspirational long-term upgrades for campaign play

### Standalone 9 XP Build Rules

- Budget is exactly 9 XP (standalone mode grant).
- All cards must come from the player's owned card pool: Core ×3 (3rd incomplete) + Dunwich cycle + Edge of the Earth Investigator + Drowned City Investigator + Return to Night of the Zealot.
- Do NOT use cards from Investigator Starter Decks (Harvey, Jacqueline, Nathaniel, Stella, Winifred) — those are kept as prebuilt pick-up-and-play decks.
- Do NOT use cards that are already in another investigator's built deck (e.g., Grotesque Statue is in Jacqueline's starter).
- Default build is tuned for Excelsior. Blob/Film Fatale variants note what changes from default.
- Tables use `table-layout:fixed` with colgroup: XP 5%, Remove 25%, Add 25%, Why 45%.

### Scenario Team Recommendations

Each scenario page (`scenarios/*.html`) includes 3 recommended teams for 3-player play:

- **Team 1 (ideal — prebuilt/starter decks):** The best team using only physically built decks (Rex, Roland, Zoey, Jenny, Agnes) and starter decks (Nathaniel, Harvey, Winifred, Jacqueline, Stella). Ready to play immediately.
- **Team 2 (alternate — expanded pool):** Best team from the Team 1 pool PLUS any investigator with a decklist on ArkhamDB (deck page exists on the Dashboard). Doesn't have to include them — just expands the options. Pick the strongest different combo.
- **Team 3 (theoretical best — requires deckbuilding):** The strongest team from the full owned investigator pool (Core, Dunwich, Edge of the Earth, Drowned City). No starters. Requires building new decks from scratch.

**Constraint:** Roland and Zoey share a physical deck (8-card swap). They cannot appear on the same team in Team 1 or Team 2.

The overview table on `investigators/index.html` shows Team 1 for each scenario.

### Campaign Team Recommendations

Each campaign guide (`campaigns/*.html`) includes team recommendations for both 3-player and 2-player.

**3-player teams** follow a 3-tier system:
- **Team 1 (best):** From prebuilt + starter deck pool only.
- **Team 2 (alternate):** Different composition from prebuilt + starter + expanded pool (Pete, Lily).
- **Team 3 (co-released):** Best team using only investigators that co-released with that campaign cycle. Thematic pick — may require decks you don't own.

**2-player teams** use a different framework:
- **Team 1 (Specialist):** Dedicated fighter + dedicated cluever from prebuilt/starter pool. Maximum efficiency when each person focuses on their role.
- **Team 2 (Generalist):** Two flex investigators from expanded pool (includes Pete, Lily). Both can fight and clue. Maximum adaptability when things go wrong or investigators get separated.
- **Team 3 (Synergy):** Two investigators whose abilities mechanically combo with each other from prebuilt/starter/expanded pool. The "1+1=3" pair — pick for the interaction, not just individual strength.
- **Team 4 (Theoretical best):** Strongest pair from full owned pool (Core, Dunwich, Edge of the Earth, Drowned City). Requires deckbuilding.

The campaign landing page (`campaigns/index.html`) shows all teams at a glance in sortable tables.
