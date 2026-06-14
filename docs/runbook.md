# Runbook: SpotMyMeal

## Prerequisites

- Python 3.8+
- pip

## Quick Start

```powershell
# Navigate to Flask directory
cd Flask

# Install dependencies
pip install -r requirements.txt

# Start server
python app1.py

# Open in browser
# http://127.0.0.1:5000/
```

The app must run from inside the `Flask/` directory so the CSV path resolves correctly.

## Files

| Path | Purpose |
|------|---------|
| `Flask/app1.py` | Flask application with recommendation logic |
| `Flask/restaurant1.csv` | Dataset (3,225 restaurants) |
| `Flask/templates/` | Jinja2 templates (index, recommend, result, error) |
| `Flask/static/` | Images and static assets |
| `Flask/requirements.txt` | Python dependencies |
| `Model/Final_Development_Phase.ipynb` | Model building notebook |

## Routes

| Route | Method | Description |
|-------|--------|-------------|
| `/` | GET | Home page |
| `/recommend` | GET | Search form with filters |
| `/result` | POST | Recommendation results |
| `/analytics` | GET | Rating histogram (no template) |

## API

POST to `/result` with form data:
- `restaurant_name` (required): Exact or partial name
- `cuisine` (optional): Cuisine substring filter
- `budget` (optional): Max cost per person
- `rating` (optional): Minimum Mean Rating

Returns HTML page with top 10 recommendations or error page.

## Dataset

`restaurant1.csv` has 3,225 rows with 5 columns:

| Column | Type | Description |
|--------|------|-------------|
| name | string | Restaurant name |
| rate | float | Aggregate rating (1-5) |
| Mean Rating | float | Mean of individual ratings (1-5) |
| cuisines | string | Comma-separated cuisine types |
| cost | float | Cost for two (INR) |

Mean Rating range: 1.0-5.0, Cost range: 1-950 INR.

## Troubleshooting

| Problem | Cause | Fix |
|---------|-------|-----|
| CSV not found | Running outside Flask/ dir | `cd Flask` before `python app1.py` |
| No results for a known restaurant | CSV has 3,225 entries; name may differ | Try partial name or check CSV |
| TF-IDF is slow | Matrix recomputed per request | Pre-compute and save to pickle for production |
| Port 5000 in use | Another app on same port | Change port: `app.run(port=5001)` |
| Template rendering errors | Jinja2 syntax mismatch | Verify Flask version: `pip show flask` |

## Rollback

- Revert to previous commit: `git revert HEAD`
- Restore backup CSV: `git checkout -- Flask/restaurant1.csv`

## Escalation

- App crash on startup: Check `requirements.txt` for missing packages
- Data corruption in CSV: Restore from git or regenerate from source
- Feature requests: Open issue on GitHub repository
