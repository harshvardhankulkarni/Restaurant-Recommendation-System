<!-- GSD -->
# Configuration: SpotMyMeal

## Dependencies

32 packages pinned in `Flask/requirements.txt`. Full table:

| Package | Version | Purpose |
|---------|---------|---------|
| Flask | 3.1.0 | Web framework |
| pandas | 2.2.3 | CSV loading, DataFrame operations |
| numpy | 2.2.5 | Numerical operations |
| scikit-learn | 1.6.1 | TfidfVectorizer, cosine_similarity |
| Jinja2 | 3.1.6 | Template engine (Flask dependency) |
| Werkzeug | 3.1.3 | WSGI toolkit (Flask dependency) |
| matplotlib | 3.10.3 | Analytics histogram generation |
| seaborn | 0.13.2 | Statistical plotting (imported but not used) |
| plotly | 6.0.1 | Interactive charts (imported but not used) |
| nltk | 3.9.1 | Natural language toolkit (imported but not used) |
| tqdm | 4.67.1 | Progress bars (imported but not used) |
| joblib | 1.5.0 | Model persistence (imported but not used) |
| click | 8.2.0 | CLI framework (Flask dependency) |
| blinker | 1.9.0 | Signal support (Flask dependency) |
| itsdangerous | 2.2.0 | Data signing (Flask dependency) |
| MarkupSafe | 3.0.2 | HTML escaping (Jinja2 dependency) |
| scipy | 1.15.3 | Scientific computing (scikit-learn dependency) |
| threadpoolctl | 3.6.0 | Thread control (scikit-learn dependency) |
| contourpy | 1.3.2 | Contour plotting (matplotlib dependency) |
| cycler | 0.12.1 | Color cycling (matplotlib dependency) |
| fonttools | 4.58.0 | Font handling (matplotlib dependency) |
| kiwisolver | 1.4.8 | Constraint solving (matplotlib dependency) |
| packaging | 25.0 | Version handling (matplotlib dependency) |
| pillow | 11.2.1 | Image processing (matplotlib dependency) |
| pyparsing | 3.2.3 | Parsing engine (matplotlib dependency) |
| python-dateutil | 2.9.0.post0 | Date handling (pandas/matplotlib dep) |
| pytz | 2025.2 | Timezone support (pandas dependency) |
| tzdata | 2025.2 | Timezone database (pandas dependency) |
| six | 1.17.0 | Python 2/3 compat (dateutil dependency) |
| colorama | 0.4.6 | Terminal color (indirect dependency) |
| narwhals | 1.39.0 | DataFrame library (indirect dependency) |
| regex | 2024.11.6 | Advanced regex (nltk dependency) |

### Not-actively-used dependencies

Several packages in `requirements.txt` are imported in the notebook (`Final_Development_Phase.ipynb`) but not in `app1.py`: seaborn, plotly, nltk, tqdm, joblib. They are safe to leave in the requirements file but are not required for the Flask app to run.

## Inline Configuration

All configuration for the Flask app is hardcoded in `Flask/app1.py`.

### TF-IDF Parameters (`app1.py:45`)

```python
tfidf = TfidfVectorizer(stop_words='english')
```

| Parameter | Current Value | Effect |
|-----------|---------------|--------|
| `stop_words` | `'english'` | Removes English stopwords ("the", "and", "for") from cuisine text |
| `analyzer` | `'word'` (default) | Tokenizes on word boundaries |
| `ngram_range` | `(1, 1)` (default) | Unigrams only — no phrase detection |
| `max_df` | `1.0` (default) | No term frequency cutoff |
| `min_df` | `1` (default) | Includes terms that appear in a single document |
| `max_features` | `None` (default) | No vocabulary size limit |
| `norm` | `'l2'` (default) | Euclidean norm for vector scaling |

### Recommendation Pipeline (`app1.py:25-98`)

| Behavior | Current Setting | Location |
|----------|----------------|----------|
| Candidate pool size | Top 50 (excluding self) | Line 54 |
| Results returned | Top 10 (after filters + sort) | Line 98 |
| Sort order | Mean Rating descending | Line 96 |
| Duplicate handling | `drop_duplicates(subset=['name'])` | Line 95 |
| Filter relaxation | Cuisine filter removed if empty result set | Lines 85-89 |
| Budget default (when missing/invalid) | 9999999 (effectively unlimited) | Lines 62-65 |
| Rating default (when missing/invalid) | 0.0 (effectively no minimum) | Lines 67-70 |

### CSV Configuration (`app1.py:13`)

```python
zomato_df = pd.read_csv('restaurant1.csv')
```

- **Path**: Relative to the working directory — must run `python app1.py` from inside `Flask/`
- **Encoding**: System default (UTF-8 compatible for Indian cuisine names)

### Dataset Schema

File: `Flask/restaurant1.csv`

| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `name` | string | No | Restaurant name |
| `rate` | float64 | Yes | Aggregate rating out of 5.0 |
| `Mean Rating` | float64 | Yes | Mean of individual ratings out of 5.0 |
| `cuisines` | string | Yes (filled with empty string) | Comma-separated cuisine tags |
| `cost` | float64 | Yes (filled with 0) | Estimated cost for two in INR |

Column normalization at startup (`app1.py:16-19`):
- `name_lower`: strip + lowercase copy of name (for matching)
- `cuisines`: nulls filled with `''`, cast to string
- `cost`: coerced to numeric, nulls filled with `0`
- `Mean Rating`: coerced to numeric, nulls filled with `0`

### Flask Server Configuration (`app1.py:153-154`)

```python
app.run(debug=True)
```

| Setting | Value | Effect |
|---------|-------|--------|
| `debug` | `True` | Auto-reload on code changes, detailed error pages |
| `host` | `127.0.0.1` (default) | Localhost only |
| `port` | `5000` (default) | Standard Flask dev port |
| `threaded` | `True` (default in Flask 3.x) | Handles concurrent requests |

To change port or host:

```python
app.run(debug=True, host='0.0.0.0', port=5001)
```

### Template Variables

`result.html` receives:

| Variable | Type | Source |
|----------|------|--------|
| `recommended_restaurants` | list[dict] | `top_restaurants.to_dict('records')` |
| `search_name` | string | Original user input, passed through |

Each dict in `recommended_restaurants` has keys: `name`, `rate`, `Mean Rating`, `cuisines`, `cost`.

`error.html` receives:

| Variable | Type | Source |
|----------|------|--------|
| `message` | string | Context-specific error message from `result()` |
| `show_back` | boolean | Always `True` for error pages rendered from `result()` |

### Analytics Configuration (`app1.py:146-150`)

```python
zomato_df['Mean Rating'].hist()
plt.savefig('static/ratings.png')
```

- Histogram uses matplotlib defaults (10 bins, auto-range)
- Output file: `Flask/static/ratings.png` (overwritten on every request)
- Template rendered: `analytics.html` (does not exist — known gap)

## Environment Variables

The app requires no environment variables. All settings are inline in `app1.py`.

## Theme / UI Configuration

The UI uses CSS custom properties defined in each template's `<style>` block. No global stylesheet. To change the visual theme, update the `:root` variables in each template:

```css
:root {
  --bg: #eef2f7;
  --surface: #eef2f7;
  --text: #1e293b;
  --muted: #64748b;
  --primary: #6366f1;
  --primary-2: #3b82f6;
  --radius: 22px;
}
```

Changes must be replicated across all 4 templates. Font: Poppins (Google Fonts). Icons: Font Awesome 6.5.0.
