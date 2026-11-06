# dsa405-project
# dsa405-project

**DSA 405 · Fall 2026 · Avery Atkinson (alatkin3)**

## Project question
Do restaurants in lower-income NYC zip codes get more critical health-code violations than restaurants in higher-income zip codes?

## Data sources
1. **NYC DOHMH Restaurant Inspection Results**: NYC Open Data, Socrata API, dataset `43nn-pn8j`. One row per violation cited.
2. **U.S. Census Bureau ACS 5-year, Table S1901**: median household income (`S1901_C01_012E`) by ZIP Code Tabulation Area (ZCTA), via the Census Data API (free key required).

This product uses the Census Bureau Data API but is not endorsed or certified by the Census Bureau.

## P3: Acquisition & Integration
Notebook: `DSA405_002_FA26_P3_alatkin3.ipynb`

- **Acquisition:** collects both sources with rate-limited (2 s between requests), cached code. Raw responses are saved to `data/raw/` and re-runs read from disk. NYC data is paginated with `$limit`/`$offset` and checked against the server's row count. The code fails loudly on timeouts, non-200 responses, non-JSON Census replies, and missing columns (demonstrated in Section 1.6).
- **Join:** restaurants (one row per restaurant) left-joined to Census income (one row per ZCTA) on zip code, many-to-one, with `validate="m:1"`. 27,463 restaurants in, 27,463 out; 99.5% of restaurants with a zip code matched a ZCTA.
- **Verification:** `verify()` runs 7 checks (row count, key uniqueness, cardinality, income range, no lost restaurants, match rate, key format). It is also run on a deliberately broken fan-out join and an inner-join "wreck" to show it catches both.

## How to run
1. Open the notebook in Google Colab.
2. Add your Census API key as a Colab Secret named `CENSUS_KEY` (key icon in the left sidebar, Notebook access on).
3. Runtime > Restart and run all. The first run downloads about 170 MB and takes a few minutes.

The `data/` folder is not stored in this repo because the raw files are large. The notebook re-creates `data/raw/` (untouched API responses) and `data/clean/` (cleaned and joined tables) when it runs. Raw files are never modified.
