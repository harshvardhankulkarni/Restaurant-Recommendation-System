<!-- GSD -->
# SpotMyMeal - Restaurant Recommendation Engine

Content-based restaurant recommendation system. Enter a restaurant name and get similar places ranked by cuisine similarity, filtered by your preferences.

**Live demo**: https://harshvardhankulkarni.github.io/Restaurant-Recommendation-System/

**Tech stack**: Flask 3.1.0, scikit-learn 1.6.1 (TF-IDF + cosine similarity), pandas 2.2.3, numpy 2.2.5, Jinja2 3.1.6, matplotlib 3.10.3

## How It Works

1. User enters a restaurant name on the search form
2. App finds the matching restaurant (exact match, partial name fallback)
3. TF-IDF vectorizer converts cuisine text to numerical vectors across all 3,225 restaurants
4. Cosine similarity scores identify the 50 most cuisine-similar restaurants
5. Optional filters applied: cuisine type substring, max budget, minimum rating
6. Cuisine filter relaxes automatically if strict filters return zero results
7. Results deduplicated, sorted by Mean Rating descending, top 10 returned

## Quick Start

```powershell
cd Flask
pip install -r requirements.txt
python app1.py
# Open http://127.0.0.1:5000/
```

Run from inside `Flask/` so the CSV path resolves correctly.

## Project Structure

```
├── Flask/
│   ├── app1.py                 # Flask application (154 lines, all logic here)
│   ├── restaurant1.csv         # Dataset: 3,225 restaurants, 5 columns
│   ├── requirements.txt        # 32 pinned dependencies
│   ├── templates/              # Jinja2 HTML templates
│   │   ├── index.html          # Home page (neumorphism UI)
│   │   ├── recommend.html      # Search form with cuisine/budget/rating filters
│   │   ├── result.html         # Top 10 recommendation results
│   │   └── error.html          # Error page (not found, no matches, empty input)
│   ├── static/                 # Static assets (hero background images)
│   └── Final_Development_Phase.ipynb  # Dev notebook
├── Model/
│   └── Final_Development_Phase.ipynb  # Model building notebook
├── docs/                       # GSD-standard documentation
│   ├── ARCHITECTURE.md
│   ├── GETTING-STARTED.md
│   ├── DEVELOPMENT.md
│   ├── TESTING.md
│   └── CONFIGURATION.md
├── index.html                  # GitHub Pages landing page
└── .gitignore
```

## Routes

| Route | Method | Description |
|-------|--------|-------------|
| `/` | GET | Home page |
| `/recommend` | GET | Search form with cuisine/budget/rating filters |
| `/result` | POST | Process form, return top 10 recommendations or error |
| `/analytics` | GET | Generate and display rating distribution histogram |

## Dataset

`Flask/restaurant1.csv`: 3,225 restaurants, 5 columns.

| Column | Type | Description |
|--------|------|-------------|
| `name` | string | Restaurant name |
| `rate` | float | Aggregate rating (1.0-5.0) |
| `Mean Rating` | float | Mean of individual ratings (1.0-5.0) |
| `cuisines` | string | Comma-separated cuisine types |
| `cost` | float | Cost for two in INR (1-950) |

Cuisines include North Indian, Chinese, South Indian, Mughlai, Italian, Thai, Fast Food, Continental, Biryani, Seafood, and more.

## Authors

Vishwajeet Sawant & Harshvardhan Kulkarni

_Demo project. Not intended for production deployment._
