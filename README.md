# Patek Philippe Data Engineering Pipeline

**Business Data Management course project | EDHEC**

Fully containerized data pipeline for a BigQuery + Docker assignment: ingests Patek Philippe watch listing data, enriches prices with historical exchange rates, optionally trains a model to predict resale value, and feeds a live dashboard.

## Problem

Raw watch listing data needs to go from scattered BigQuery tables to clean, FX-adjusted, analysis-ready data — reproducibly, without manual steps, and without leaking credentials.

## Approach

**Storage layers (BigQuery):**
- `patek_raw` — raw ingestion (unprocessed backup)
- `patek` — production table (cleaned & enriched)
- `fx_rates` — historical exchange rates

**Pipeline (Docker):**
1. **Extraction & cleaning** — `data.py` pulls raw BigQuery data, sanitizes it, saves a CSV snapshot.
2. **FX enrichment** — `fx_rates.py` fetches historical exchange rates, updates `price_EUR` in BigQuery and locally.
3. **Modeling (optional)** — `model.py` trains a Random Forest to predict prices.
4. **Visualization** — Jupyter notebooks for ad-hoc analysis; Looker Studio dashboard reading live from BigQuery.

**Reproducibility:** everything runs inside Docker via Make commands; credentials are injected through a mounted `secrets/` volume, never committed.

## Usage

```bash
# Full pipeline run
docker compose run --rm --service-ports app bash
python -m patek_analysis.data
python -m patek_analysis.fx_rates
python -m patek_analysis.model  # optional

# Notebook exploration
docker compose run --rm --service-ports app make notebook
```

Dashboard (Looker Studio) reads directly from the `patek` BigQuery table — no manual refresh needed after a pipeline run.

## Stack

Python 3.12 · Docker & Docker Compose · Google BigQuery · Pandas · Scikit-Learn · Google Cloud SDK · Looker Studio
