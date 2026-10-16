# ME204 Final Project: Inflation and Youth Unemployment Across Three Countries

**Author:** [Alihan Kilic](https://github.com/aliskilic)

## Project question

**How did consumer-price inflation and youth unemployment differ across Italy, Türkiye, and the United Kingdom from 2010 to the latest common year available?**

The project implements a reproducible pipeline that collects annual observations from the World Bank Indicators API, preserves the original responses, transforms them into an analysis-ready country-year table, and explores differences across the three countries over time.

The final year was not assumed in advance. NB01 calculated the intersection of the non-missing years available across all three countries and both indicators. This identified **2025** as the latest common year, so the completed analysis covers **2010–2025**.

## Data source and indicators

The sole data source is the public World Bank Indicators API, version 2. The API returns structured JSON responses and does not require an API key, login, or other credential.

The project uses:

- `FP.CPI.TOTL.ZG` — Inflation, consumer prices (annual %)
- `SL.UEM.1524.ZS` — Unemployment, youth total (% of total labour force ages 15–24), modelled ILO estimate

The countries are:

- Italy (`ITA`)
- Türkiye (`TUR`)
- United Kingdom (`GBR`)

Youth unemployment is measured among the labour force aged 15–24, rather than the entire population in that age group. The age range is part of the selected World Bank indicator definition.

NB01 requests the period 2010–2026 so that the latest common year can be derived from the returned observations rather than fixed beforehand. The API returned complete annual observations through 2025 for all six country-indicator combinations.

No manually downloaded static dataset is used as either a main or supplementary source.

## Repository workflow

Run the notebooks in the following order.

### 1. `notebooks/NB01-Data-Collection.ipynb`

**Reads**

- World Bank Indicators API responses

**Does**

- sends one HTTP GET request for each indicator;
- requests all three countries and the period 2010–2026;
- checks the response status and JSON structure;
- validates pagination, countries, indicator codes, missing values, and annual coverage;
- calculates 2025 as the latest common year;
- saves the complete response body for each indicator without reshaping it.

**Writes**

- `data/raw/world_bank_inflation.json`
- `data/raw/world_bank_youth_unemployment.json`

### 2. `notebooks/NB02-Data-Transformation.ipynb`

**Reads**

- `data/raw/world_bank_inflation.json`
- `data/raw/world_bank_youth_unemployment.json`

**Does**

- extracts the country, year, indicator, and numerical value fields;
- combines both raw responses into one long intermediate table;
- checks required fields, data types, missing values, and unique country-year-indicator keys;
- reshapes the indicators into separate columns without aggregation;
- validates the final country-year structure and annual coverage.

**Writes**

- `data/processed/country_year_indicators.csv`

The processed file contains **48 rows and 5 columns**. Each row represents one country in one year, with inflation and youth unemployment stored in separate numerical columns. Each country contributes 16 annual observations covering 2010–2025. The table contains no missing values or duplicate country-year rows.

### 3. `notebooks/NB03-aliskilic-Data-Analysis.ipynb`

**Reads**

- `data/processed/country_year_indicators.csv`

**Does**

- verifies the processed input before analysis;
- compares inflation and youth-unemployment trajectories across the three countries;
- identifies observed maxima, minima, endpoint changes, and differences in peak timing;
- produces the evidence used for the three central findings;
- keeps the analysis descriptive and does not claim causal relationships.

**Writes**

- `docs/assets/inflation_all_countries.png`
- `docs/assets/inflation_italy_uk.png`
- `docs/assets/youth_unemployment.png`

NB03 does not collect data or create another processed dataset. It reads the output of NB02 directly and saves the three presentation figures used on the project page.

### 4. `docs/aliskilic.md`

Presents the focused question, data source, three supported findings, overall answer, and limitations for a general reader.

### 5. `docs/index.md`

Links to the individual project page.

## How to reproduce the project

1. Open the repository in Nuvolos and work from the `notebooks/` directory so the notebooks' relative paths resolve correctly.
2. Run `NB01-Data-Collection.ipynb`.
3. Confirm that both JSON files were created under `data/raw/`.
4. Run `NB02-Data-Transformation.ipynb`.
5. Confirm that `data/processed/country_year_indicators.csv` was created.
6. Run `NB03-aliskilic-Data-Analysis.ipynb`.
7. Confirm that the three PNG figures were created under `docs/assets/`.
8. Open `docs/index.md` and follow the link to `docs/aliskilic.md`.

Each notebook should be run from a restarted kernel in the order shown above.

## Environment and dependencies

The project was developed and tested in the Nuvolos course `base` environment using Python 3.13.5.

External Python packages:

- `requests` — sends requests to the World Bank Indicators API in NB01;
- `pandas` — prepares and analyses tabular data in NB02 and NB03;
- `matplotlib` — creates and saves the figures in NB03.

Standard-library modules:

- `json`
- `pathlib`

No API credentials are required, so no `.env` file is needed to run the project. Secrets should not be added to the repository.

## Analytical scope

The project is exploratory and descriptive. It compares annual national indicators, turning points, endpoint changes, and peak timing. It does not estimate causal effects or account for regional, demographic, institutional, or within-year differences.

## Publication

This public portfolio repository contains the project notebooks, data snapshots,
analysis and figures. The final project page is provided as `docs/aliskilic.md`
and is linked from `docs/index.md`. The figures used on the page are stored
under `docs/assets/`.

## Project status

The complete pipeline is implemented:

- NB01 collects, validates, and preserves the original API responses.
- NB02 creates and validates the 48-row analysis-ready country-year table.
- NB03 performs the exploratory analysis and regenerates the three figures.
- `docs/aliskilic.md` presents the findings and limitations.
- `docs/index.md` links to the project page.
