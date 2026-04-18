# SpotMyMeal - Restaurant Recommendation System

SpotMyMeal is a Flask-based restaurant recommendation web app that suggests similar restaurants using a content-based filtering approach.

The app recommends restaurants by comparing cuisine similarity (TF-IDF + cosine similarity), then applies optional filters for cuisine, budget, and minimum rating.

## Features

- Content-based restaurant recommendation engine
- Case-insensitive restaurant search with partial-name fallback
- Filtering by:
  - preferred cuisine
  - maximum budget per person
  - minimum rating
- Top results sorted by rating
- User-friendly error handling for no-match scenarios
- Modern responsive dark glassmorphism UI

## Tech Stack

- Python
- Flask
- Pandas
- Scikit-learn
- HTML + CSS (Jinja templates)

## Project Structure

```text
Restaurant-Recommendation-system-main/
├── Flask/
│   ├── app1.py
│   ├── requirements.txt
│   ├── restaurant1.csv
│   ├── static/
│   └── templates/
│       ├── index.html
│       ├── recommend.html
│       ├── result.html
│       └── error.html
├── Model/
│   └── Final_Development_Phase.ipynb
└── Project Excecution Files/
```

## Setup Instructions

### 1. Clone repository

```bash
git clone <your-repo-url>
cd Restaurant-Recommendation-system-main
```

### 2. Create and activate virtual environment (recommended)

```bash
python -m venv .venv
```

- Windows (PowerShell):

```powershell
.\.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
pip install -r Flask/requirements.txt
```

### 4. Run the Flask app

Important: run from inside the `Flask` folder so relative dataset paths resolve correctly.

```bash
cd Flask
python app1.py
```

### 5. Open in browser

```text
http://127.0.0.1:5000/
```

## How It Works

1. User enters a restaurant name and optional filters.
2. App finds matching restaurant (exact + partial fallback).
3. Cuisine text is vectorized using TF-IDF.
4. Cosine similarity identifies similar restaurants.
5. Filters are applied for cuisine, budget, and rating.
6. Top matches are shown on the results page.

## Current Routes

- `/` - Home page
- `/recommend` - Search/filter form page
- `/result` - Recommendation results page (POST)
- `/analytics` - Histogram route in backend (template not included currently)

## Notes

- Core recommendation logic and backend behavior are preserved.
- UI has been redesigned for a modern dark glassmorphism look.
- Dataset used: `Flask/restaurant1.csv`.

## Future Improvements

- Add test suite for recommendation behavior and edge cases
- Add pagination for large result sets
- Add analytics page template to support `/analytics`
- Containerize app with Docker for easy deployment

## Author

Amar Patil
