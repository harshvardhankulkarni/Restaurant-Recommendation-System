<!-- GSD -->

# Restaurant Recommendation System (SpotMyMeal) — Architecture

## Context and Goals

Content-based restaurant recommendation engine using TF-IDF vectorization and cosine similarity. Flask web application serving 3,225 restaurants from a CSV dataset. Portfolio demo showcasing recommendation systems with web deployment.

## Architecture

Flask MVC web application with in-memory recommendation engine. No database — data loaded from CSV on startup. TF-IDF similarity matrix recomputed per request.

## Data Flow

```
CSV Dataset (3,225 restaurants, 5 columns)
  → Pandas DataFrame
  → TF-IDF vectorizer on cuisine text (stop_words='english')
  → Cosine similarity matrix
  → User selects restaurant name via web form
  → Top 50 candidates by similarity
  → Filter by cuisine, budget, rating
  → Auto-relax cuisine filter if no results
  → Deduplicate and sort by Mean Rating
  → Top 10 recommendations displayed
```

## Routes

| Route | Method | Description |
|-------|--------|-------------|
| `/` | GET | Home page with hero section |
| `/recommend` | GET | Recommendation form with cuisine, budget, rating filters |
| `/recommend` | POST | Process input, return results page |
| `/analytics` | GET | Rating distribution histogram |

## Components

| File | Role |
|------|------|
| `Flask/app1.py` | Flask application: routes, TF-IDF engine, filtering logic |
| `Flask/restaurant1.csv` | Dataset: 3,225 restaurants with name, rate, rating, cuisines, cost |
| `Flask/requirements.txt` | Pinned dependencies (32 packages) |
| `Flask/templates/` | Jinja2 templates (home, form, results, error) |
| `Flask/static/` | Static assets (images) |
| `Model/Final_Development_Phase.ipynb` | Model development notebook |
| `Project Excecution Files/` | Development backup files |

## Dataset Schema

| Column | Description |
|--------|-------------|
| `name` | Restaurant name |
| `rate` | Rating out of 5 (e.g., "4.1/5") |
| `Mean Rating` | Calculated mean rating |
| `cuisines` | Cuisine tags (comma-separated) |
| `cost` | Cost for two people |

## Design Decisions

| Decision | Rationale |
|----------|-----------|
| Content-based filtering | No user rating data needed, works with restaurant attributes only |
| TF-IDF on cuisine text | Simple, effective for text-based similarity |
| Cosine similarity | Standard metric for text vector comparison |
| Filters applied post-similarity | Users can narrow recommendations without recomputing |
| Auto-relax empty filters | Prevents zero results when cuisine filter is too strict |

## Trade-offs

- No collaborative filtering — ignores user behavior patterns
- Cold start: new restaurants with no cuisine data won't be found
- Limited to cuisine-based similarity — no price, location, or ambiance matching
- No persistent database — data reloads on each Flask restart
- Similarity matrix recomputed per request (not cached)

## File Organization

```
Restaurant-Recommendation-System/
├── Flask/
│   ├── app1.py
│   ├── restaurant1.csv
│   ├── requirements.txt
│   ├── templates/
│   └── static/
├── Model/
│   └── Final_Development_Phase.ipynb
├── Project Excecution Files/
│   └── ...
├── index.html
└── docs/
    ├── ARCHITECTURE.md
    ├── GETTING-STARTED.md
    ├── DEVELOPMENT.md
    ├── TESTING.md
    └── CONFIGURATION.md
```
