# SpotMyMeal - Restaurant Recommendation System

Demo project. Content-based restaurant recommendation engine using TF-IDF and cosine similarity. Built with Flask.

## Tech Stack

Python, Flask, Pandas, scikit-learn, Jinja2, HTML/CSS

## Dataset

3,225 restaurants with name, rating, cuisines, and cost columns. Cuisines include North Indian, Chinese, South Indian, Mughlai, Italian, Thai, Fast Food, and more.

## Quick Start

```powershell
cd Flask
pip install -r requirements.txt
python app1.py
# Open http://127.0.0.1:5000/
```

Run from inside `Flask/` so the CSV path resolves.

## How It Works

1. User enters a restaurant name and optional filters (cuisine, budget, rating)
2. App finds matching restaurant (exact match + partial name fallback)
3. TF-IDF vectorizer converts cuisine text to numerical vectors
4. Cosine similarity identifies the 50 most similar restaurants
5. Filters applied, top 10 sorted by rating

## Project Structure

```
├── Flask/
│   ├── app1.py              # Flask application
│   ├── restaurant1.csv      # Dataset
│   ├── requirements.txt
│   ├── static/               # Images
│   └── templates/            # Jinja2 templates
├── Model/
│   └── Final_Development_Phase.ipynb
├── docs/
│   ├── architecture.md
│   └── runbook.md
└── index.html                # GitHub Pages landing page
```

## Routes

- `/` - Home page
- `/recommend` - Search form with filters
- `/result` (POST) - Recommendation results
- `/analytics` - Rating histogram

## Documentation

- [Architecture](docs/architecture.md)
- [Runbook](docs/runbook.md)

## Author

Vishwajeet Sawant & Harshvardhan Kulkarni
