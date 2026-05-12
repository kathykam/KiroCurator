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

## Output

Long recommendations from the Curator and Recommender personas are written to `Output/recommendation.md`. Check that file after asking for detailed advice.
