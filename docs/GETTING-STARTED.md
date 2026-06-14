<!-- GSD -->
# Getting Started: SpotMyMeal

## Prerequisites

- Python 3.8 or later
- pip (Python package installer)
- Git (for cloning the repository)

## Installation

### 1. Clone the repository

```powershell
git clone https://github.com/harshvardhankulkarni/Restaurant-Recommendation-System.git
cd Restaurant-Recommendation-System
```

### 2. Create and activate a virtual environment (recommended)

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

On macOS / Linux:

```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

The CSV path in `app1.py` is relative to the `Flask/` directory. All commands must run from inside `Flask/`.

```powershell
cd Flask
pip install -r requirements.txt
```

This installs 32 pinned packages including Flask 3.1.0, pandas 2.2.3, scikit-learn 1.6.1, numpy 2.2.5, matplotlib 3.10.3, and Jinja2 3.1.6.

### 4. Start the server

```powershell
python app1.py
```

You will see output similar to:

```
 * Serving Flask app 'app1'
 * Debug mode: on
 * Running on http://127.0.0.1:5000 (Press CTRL+C to quit)
```

### 5. Open in browser

Navigate to http://127.0.0.1:5000/

## Using the Web Interface

### Search for recommendations

1. Click **Get Started** on the home page or navigate to `/recommend`
2. Enter a restaurant name (required) — try "Jalsa", "Spice Elephant", or "San Churro Cafe"
3. Optionally select a **Cuisine Type** filter (e.g., North Indian, Chinese, Italian)
4. Optionally set a **Maximum Budget** per person
5. Optionally set a **Minimum Rating** threshold
6. Click **Get Recommendations**

### Understanding results

- Results show top 10 restaurants ranked by Mean Rating
- Each card displays the restaurant name, cuisine types, rating, and cost per person
- Click **New Search** to try again

### Error handling

- **Empty name**: Prompts you to enter a restaurant name
- **Name not found**: Suggests checking spelling or trying another name
- **No matches**: Advises relaxing filters (budget, rating, cuisine)

## Running from VS Code

1. Open the `Flask/` folder in VS Code
2. Open `app1.py`
3. Press Ctrl+F5 or click the Run button
4. The Flask debug server starts on port 5000

## Troubleshooting

| Problem | Cause | Fix |
|---------|-------|-----|
| `FileNotFoundError: restaurant1.csv` | Running `app1.py` from outside `Flask/` | `cd Flask` first, then `python app1.py` |
| `ModuleNotFoundError: flask` | Dependencies not installed | `pip install -r Flask/requirements.txt` |
| Port 5000 in use | Another process on same port | Change port in `app1.py`: `app.run(debug=True, port=5001)` |
| No results for known restaurant | Dataset has 3,225 entries, name may differ slightly | Try partial name or check CSV for exact name |
| Slow response | TF-IDF matrix recomputed per request | Expected for 3k+ rows on first request; subsequent requests use warm cache |
| `Jinja2` template error | Template variable mismatch | Verify you're using the latest templates with the current `app1.py` |
| Analytics page shows error | `analytics.html` template does not exist | Create it or add the template reference in `app1.py` |
| matplotlib font warning | Missing Hindi/Indian fonts on some systems | Ignore; falls back to default font automatically |
