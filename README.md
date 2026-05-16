# KiroGames - Persona Menu

| Trigger | Persona | Files Used |
|---------|---------|------------|
| "Be the Rater" | GameRater | `GamingProfile/collection-trimmed.csv` only |
| "Be the Curator" | GameCurator | `GamingProfile/collection-trimmed.csv` + `GamingProfile/CurationGuide.md` + `GamingProfile/UserProfile.md` |
| "Be the Recommender" | GameRecommendations | `GamingProfile/collection-trimmed.csv` + `GamingProfile/UserProfile.md` |
| "Be the Rules Teacher" | GameRulesTeacher | `GamingProfile/collection-trimmed.csv` only |
| "Update the collection" | — | Processes `UserData/collection.csv` → updates `GamingProfile/` and `Dashboard/collection.html` |

## Descriptions

**Be the Rater** — Predicts your rating for a named board game based on your collection data. Give it a game name and it predicts your score.

**Be the Curator** — Curates game night lists and advises on what to keep, buy, or sell. Considers your full profile and curation preferences.

**Be the Recommender** — Finds new games and evaluates whether you'd enjoy them based on your preferences and rating history.

**Be the Rules Teacher** — Teaches and clarifies board game rules. Explains setup, turn flow, and scoring with focus on commonly confused rules and edge cases.

**Update the collection** — Drop a new `collection.csv` into `UserData/`, say the trigger, and the AI will trim it, update the data files, and rebuild the HTML viewer.

## Dashboard Pages

| Page | Description |
|------|-------------|
| `index.html` | Landing page with 2-column menu grid |
| `collection.html` | Sortable/filterable collection viewer with multi-select ownership filter |
| `arkham-lcg.html` | Arkham Horror LCG campaign collection dashboard |
| `arkham-official.html` | Official FFG campaigns & standalones ranked by profile fit |
| `arkham-homebrew.html` | Fan-made campaigns & scenarios ranked by profile fit |
| `arkham-ranked.html` | Combined official + homebrew rankings |
| `narrative-finder.html` | Narrative Game Finder — filter by preferences + narrative depth score (0-10) |
| `faq-<game-slug>.html` | Per-game FAQ pages — rules clarifications added as questions are asked |

**Mechanical Depth definition:** A game must have a resource you manage (time, actions, health, money, cards) that constrains decisions and forces tradeoffs. Pure puzzles (solve or don't) and random paragraph lookups (no meaningful decisions) are not games.

All pages share `styles.css` for consistent styling. Page-specific styles remain inline only when they override the shared base.

## Output

Long recommendations from the Curator and Recommender personas are written to `Output/recommendation.md`. Check that file after asking for detailed advice.
