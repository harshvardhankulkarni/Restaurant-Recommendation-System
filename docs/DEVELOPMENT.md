<!-- GSD -->
# Development: SpotMyMeal

## Project Structure

```
Flask/                          # Web application (primary development directory)
├── app1.py                     # Single-file Flask app: routes, recommendation logic, startup
├── restaurant1.csv             # Dataset in CSV format, 5 columns
├── requirements.txt            # 32 pinned Python dependencies
├── templates/                  # Jinja2 templates (4 files)
├── static/                     # Static images
├── Final_Development_Phase.ipynb  # Jupyter notebook: development history
└── TODO.md                     # Development notes and pending items

Model/                          # Standalone model work
└── Final_Development_Phase.ipynb  # Model building notebook

Project Excecution Files/       # Backup copies of all Flask app files
└── Flask/, Model/              # Mirrors structure above

docs/                           # GSD-standard documentation
├── ARCHITECTURE.md
├── GETTING-STARTED.md
├── DEVELOPMENT.md
├── TESTING.md
└── CONFIGURATION.md

index.html                      # GitHub Pages landing page
.gitignore
```

## Flask App (`Flask/app1.py`)

Single-file application (154 lines). Structure:

### Imports and Setup (lines 1-20)
- Standard library: `warnings`, `numpy`, `pandas`
- Flask: `Flask`, `render_template`, `request`
- scikit-learn: `TfidfVectorizer`, `cosine_similarity`
- Dataset loaded at module level into global `zomato_df`
- Column normalization happens once at import time

### Recommendation Function (`get_recommendations`, lines 25-98)

Core logic. Parameters: `restaurant_name`, `cuisine`, `budget`, `rating`.

Pipeline:
1. Copy the global DataFrame (avoids mutation)
2. Normalize input restaurant name (strip, lowercase)
3. Exact match on `name_lower` column, then partial substring fallback
4. Build TF-IDF matrix: `TfidfVectorizer(stop_words='english')` on all 3,225 cuisines
5. Cosine similarity: compare matched restaurant row against all rows
6. Sort descending, exclude self, take top 50 indices
7. Apply cuisine substring filter (case-insensitive)
8. Apply cost <= budget and Mean Rating >= rating filters
9. If cuisine filter kills all results, retry without cuisine filter
10. Remove duplicate names, sort by Mean Rating descending, return top 10

### Routes (lines 104-154)

4 routes:
- `home()` — renders `index.html`
- `recommend()` — renders `recommend.html` (form)
- `result()` — POST handler, calls `get_recommendations()`, renders `result.html` or `error.html`
- `analytics()` — generates matplotlib histogram, saves to `static/`, renders `analytics.html`

App starts with `app.run(debug=True)`.

## Modifying the Recommendation Logic

### Change TF-IDF parameters

In `get_recommendations()` at `Flask/app1.py:45`:

```python
tfidf = TfidfVectorizer(stop_words='english')
```

Options:
- Remove `stop_words` to keep English stop words in cuisine text
- Add `ngram_range=(1, 2)` to capture bigram cuisine phrases
- Add `max_features=500` to limit vocabulary size for performance
- Add `min_df=2` to ignore cuisines that appear in only 1 restaurant

### Change similarity metric

At `Flask/app1.py:47`, swap `cosine_similarity`:

```python
from sklearn.metrics.pairwise import euclidean_distances, manhattan_distances
# Replace cosine_similarity with euclidean_distances or manhattan_distances
```

Note: Euclidean/manhattan are distance metrics (lower = more similar). You would need to invert the sort order.

### Change candidate pool size

At `Flask/app1.py:54`, change the slice:

```python
sim_indices = [i[0] for i in sim_scores if i[0] != idx][:50]  # Change 50 to desired number
```

### Change results count

At `Flask/app1.py:98`:

```python
return filtered.head(10)  # Change 10 to desired number
```

## Adding New Features

### Add a new filter

1. Add the filter parameter to `get_recommendations()` signature (line 25)
2. Extract the value from `request.form` in `result()` (lines 117-120)
3. Apply the filter on the `filtered` DataFrame after line 82
4. Add the corresponding form field in `Flask/templates/recommend.html`

### Update the dataset

1. Edit `Flask/restaurant1.csv` — add or modify rows
2. Ensure the 5-column schema is maintained: `name,rate,Mean Rating,cuisines,cost`
3. Restart the Flask server (CSV is loaded once at import)

### Add a new template

1. Create a new `.html` file in `Flask/templates/`
2. Add a corresponding route in `app1.py`
3. Use the existing neumorphism CSS variables (defined in each template's `<style>` block) for consistent styling

### Fix the analytics page

The `/analytics` route at `Flask/app1.py:145-150` renders `analytics.html` which does not exist. To fix:

1. Create `Flask/templates/analytics.html`
2. Include a Jinja2 variable or static image reference (e.g., `<img src="{{ url_for('static', filename='ratings.png') }}">`)

## Code Style

- **Python**: Standard PEP 8. Single file, no classes, no type hints (legacy notebook-origin code)
- **HTML/CSS**: Inline CSS in each template. CSS custom properties for theming. Neumorphism design pattern. Font Awesome 6.5.0 for icons
- **Jinja2**: Template inheritance not used (all templates are standalone). Filters used: `title`, `format`. Control flow with `{% if %}`, `{% for %}`
- **Naming**: Snake case for Python variables and functions. Template variable names match DataFrame column names

## Running the Development Notebook

The file `Flask/Final_Development_Phase.ipynb` contains the original development workflow. Run it to explore the recommendation logic step by step:

```powershell
cd Flask
jupyter notebook Final_Development_Phase.ipynb
```

## Git Workflow

- Feature work happens directly on the main branch (single-developer demo project)
- Keep `requirements.txt` updated when adding dependencies
- Regenerate `restaurant1.csv` carefully — breaking changes to schema require code changes in `app1.py`
