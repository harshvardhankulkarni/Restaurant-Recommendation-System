# Architecture: SpotMyMeal Restaurant Recommendation System

## Context and Goals

SpotMyMeal is a demo content-based restaurant recommendation engine. A user enters a restaurant name they like, and the system finds similar restaurants using cuisine text similarity. Optional filters narrow results by cuisine type, budget, and minimum rating.

Goals:
- Serve recommendations in under 2 seconds
- Work with no user accounts or persistent state
- Run on a single machine with no external services

## Data Flow

```
User Browser                     Flask Server                    Dataset
     |                               |                              |
     |-- GET / -> index.html ------->|                              |
     |<-- HTML page -----------------|                              |
     |                               |                              |
     |-- GET /recommend ------------>|                              |
     |<-- recommend.html ------------|                              |
     |                               |                              |
     |-- POST /result ---------------|                              |
     |   restaurant_name             |--- Load CSV (startup) ------>|
     |   cuisine                     |                              |
     |   budget                      |--- TF-IDF on cuisines ------>|
     |   rating                      |--- cosine_similarity ------->|
     |                               |                              |
     |<-- result.html (top 10) ------|                              |
```

## Recommendation Pipeline

1. **Lookup** - Match input restaurant name (case-insensitive exact match, partial fallback)
2. **Vectorize** - TF-IDF vectorizer on `cuisines` column (stop_words='english')
3. **Similarity** - Cosine similarity matrix between all restaurants
4. **Filter** - Optional cuisine substring match, budget <= max, Mean Rating >= min
5. **Rank** - Sort by Mean Rating descending, return top 10 unique

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Framework | Flask | Lightweight, no ORM, single-process app fits the use case |
| Similarity approach | TF-IDF + cosine similarity | Simple, no training needed, works well with short text (cuisines) |
| Dataset size | 3,225 rows | Fits in memory; TF-IDF matrix computed on every request (acceptable for this scale) |
| Name matching | Exact + partial | Exact gives precision; partial provides fallback for typos |
| UI style | Glassmorphism / Neumorphism | Modern look without heavy CSS framework dependencies |
| Data format | CSV | Portable, no database setup required |

## Integration Points

- Dataset: `Flask/restaurant1.csv` (5 columns: name, rate, Mean Rating, cuisines, cost)
- Templates: 4 Jinja2 templates in `Flask/templates/`
- Static assets: `Flask/static/` (images for hero background)

## Trade-offs

- TF-IDF matrix recomputed per request: fine for 3k rows, would need caching or pre-computation at 50k+
- Cuisine-only similarity ignores location, price range, and ambience data not present in the dataset
- Single-server Flask (no uWSGI/gunicorn) limits concurrent users in production
