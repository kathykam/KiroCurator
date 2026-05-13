# KiroGames - Persona Menu

| Trigger | Persona | Files Used |
|---------|---------|------------|
| "Be the Rater" | GameRater | `GamingProfile/collection-trimmed.csv` only |
| "Be the Curator" | GameCurator | `GamingProfile/collection-trimmed.csv` + `GamingProfile/CurationGuide.md` + `GamingProfile/UserProfile.md` |
| "Be the Recommender" | GameRecommendations | `GamingProfile/collection-trimmed.csv` + `GamingProfile/UserProfile.md` |
| "Update the collection" | — | Processes `UserData/collection.csv` → updates `GamingProfile/` and `Dashboard/collection.html` |

## Descriptions

**Be the Rater** — Predicts your rating for a named board game based on your collection data. Give it a game name and it predicts your score.

**Be the Curator** — Curates game night lists and advises on what to keep, buy, or sell. Considers your full profile and curation preferences.

**Be the Recommender** — Finds new games and evaluates whether you'd enjoy them based on your preferences and rating history.

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
| `game-finder.html` | Toggleable preference filters against curated 2026 game list |

All pages share `styles.css` for consistent styling. Page-specific styles remain inline only when they override the shared base.

## Output

Long recommendations from the Curator and Recommender personas are written to `Output/recommendation.md`. Check that file after asking for detailed advice.
