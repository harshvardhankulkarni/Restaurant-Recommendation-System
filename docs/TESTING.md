<!-- GSD -->
# Testing: SpotMyMeal

No automated test suite exists for this project. All validation is manual.

## Manual Test Checklist

### 1. App Startup

| Test | Expected Result |
|------|----------------|
| Run `python app1.py` from inside `Flask/` | Server starts, no import errors, listens on port 5000 |
| Run `python app1.py` from repo root | FileNotFoundError (CSV path) |
| Open http://127.0.0.1:5000/ | Home page loads with neumorphism UI |

### 2. Route Tests

| Test | Expected Result |
|------|----------------|
| GET `/` | Returns `index.html` with hero section, CTA buttons, feature cards |
| GET `/recommend` | Returns `recommend.html` with search form and all filter dropdowns |
| GET `/recommend` — verify dropdowns | Cuisine, Budget, and Rating select elements present with correct options |
| POST `/result` with empty name field | Returns `error.html` with message: "Please enter a restaurant name to search." |
| POST `/result` with gibberish name (e.g., "zzzzz") | Returns `error.html`: "No restaurant named ... was found" |
| POST `/result` with valid name (e.g., "Jalsa") | Returns `result.html` with up to 10 restaurant cards |
| GET `/analytics` | Generates `static/ratings.png`, attempts to render `analytics.html` (will error if template missing) |

### 3. Recommendation Tests

Test with the following restaurant names (verified present in dataset):

| Restaurant Name | Expected |
|-----------------|----------|
| `Jalsa` | Returns North Indian, Mughlai, Chinese cuisine matches |
| `Spice Elephant` | Returns Chinese, North Indian, Thai cuisine matches |
| `San Churro Cafe` | Returns Cafe, Mexican, Italian cuisine matches |
| `Addhuri Udupi Bhojana` | Returns South Indian, North Indian cuisine matches |
| Partial name: `Jal` | Partial fallback finds "Jalsa" — works |
| Partial name: `Spice` | Partial fallback finds "Spice Elephant" — works |

### 4. Filter Tests

Use a known restaurant (e.g., "Jalsa") as base name.

| Filter Setting | Expected |
|----------------|----------|
| Cuisine: "north indian" | Results filtered to restaurants with North Indian cuisine |
| Cuisine: "chinese" | Results filtered to restaurants with Chinese cuisine |
| Budget: 300 | Only restaurants with cost <= 300 INR shown |
| Budget: 1000 | Only restaurants with cost <= 1000 INR shown |
| Rating: 4.0 | Only restaurants with Mean Rating >= 4.0 |
| Rating: 4.5 | Likely returns empty — relaxes cuisine filter, shows "no_matches" |
| All filters active (Cuisine: Italian, Budget: 500, Rating: 4.0) | Narrowest result set |
| Cuisine filter that kills all results | Auto-relaxes: removes cuisine filter, shows results matching budget+rating |

### 5. Edge Cases

| Test | Expected |
|------|----------|
| Budget set to 0 | No restaurants cost 0, returns "no_matches" (unless dataset has free restaurants) |
| Rating set to 5.0 | Minimal results (very few restaurants have Mean Rating 5.0) |
| Restaurant name with leading/trailing spaces | Stripped in `get_recommendations()` — should match |
| Restaurant name in uppercase ("JALSA") | Lowercased in `get_recommendations()` — should match |
| Restaurant name with special characters | Depends on dataset content; partial fallback may help |
| Multiple restaurants with same name | `drop_duplicates(subset=['name'])` ensures unique names in results |

### 6. Analytics Route

| Test | Expected |
|------|----------|
| Visit `/analytics` | Route runs matplotlib, saves `static/ratings.png`, renders template |
| If template error | The `analytics.html` template file is missing from `Flask/templates/` — this is a known development gap |

## Test Dataset

The dataset contains 3,225 restaurants. Sample records used for testing:

```
Jalsa               | 4.1 | 3.81 | north indian mughlai chinese | 800
Spice Elephant      | 4.1 | 3.81 | chinese north indian thai    | 800
San Churro Cafe     | 3.8 | 3.37 | cafe mexican italian         | 800
Addhuri Udupi Bhojana | 3.7 | 3.22 | south indian north indian | 300
```

These four restaurants confirm that recommendation sorting, cuisine filtering, and budget filtering all work correctly with known baseline values.

## Adding Automated Tests

To add automated tests in the future:

1. Create `Flask/tests/` directory
2. Extract `get_recommendations()` logic into a testable module (mock the CSV load)
3. Use `pytest` for unit tests on the recommendation function
4. Use Flask's test client for route integration tests
5. Test matrix: 4 routes x 3 states (success, empty, error) = 12 route tests
6. Test the 3 error message paths in `result()`: empty name, not found, no matches
