# Shoreline change-rate demo (Portuguese coast)

A small demonstration of a transect-based shoreline change analysis, built on a
satellite-derived shoreline dataset for the Portuguese coast.

**This is a test on Portuguese data, not England and Wales data.** It shows the
method (chunked processing, per-transect trend fitting, coverage checks) that
would be applied to a different dataset.

## Data

- File: `PRT_SHODIST_all.csv` (not included in this repository; too large for GitHub)
- Coverage: 11,062 transects, satellite-derived shoreline distances, 22 March 1984
  to 29 December 2024
- 15,613 date columns; about 9% of cells contain measurements
- Metadata columns: `name`, `Class`, `Desig_APA`, `Municip`
- Source: [confirm before publishing; the file came without a data dictionary]

## Method

1. Read the header to identify the date columns, without loading the full file.
2. Read the data in chunks of 500 transects to keep memory use low.
3. For each transect, keep the valid numeric measurements.
4. Require at least 10 observations spanning at least 5 years.
5. Fit a linear trend of distance against time. The slope is the change rate (metres per year).
6. Classify each transect as erosion, accretion or stable, using ±0.5 m/yr.
7. Summarise coverage per year and the distribution of rates.

## Results

| Measure | Value |
|---|---|
| Transects | 11,062 |
| Rates calculated | 11,035 |
| Insufficient data | 27 |
| Stable (within ±0.5 m/yr) | 5,853 |
| Erosion | 2,700 |
| Accretion | 2,482 |
| Median rate | −0.053 m/yr |

Measurements per year rise from about 88,000–132,000 in the late 1980s to about
1.2–1.4 million in the 2020s. The sharp increase begins around 2012.

Outputs are in `outputs/`:
- `transect_rates.csv`: one row per transect
- `coverage_by_year.csv`: valid measurements per year
- `rate_distribution.png`: histogram of rates (clipped to ±10 m/yr for display)
- `coverage_by_year.png`: coverage over time

## Limitations

- **Sign convention is assumed.** Positive rates are read as seaward growth. Check
  the data dictionary before interpreting the direction of change.
- **Units are assumed to be metres.** The file does not state them.
- **Linear trends only.** Storms, seasonal change and sudden events are not modelled.
- **Uneven coverage over time.** Early years have far fewer measurements, so rates
  for transects measured mainly in those years are less reliable.
- **Possible partial final year.** The 2024 count drops compared with 2023. Check
  monthly counts before drawing conclusions from it.
- **Outliers are not removed.** Extreme values are clipped for display only.

## How to run

Open `shoreline_analysis.ipynb` in Jupyter or VS Code, after creating the
environment:

```
conda create -n shoreline python=3.11 pandas numpy matplotlib jupyter ipykernel -c conda-forge
conda activate shoreline
```

Place `PRT_SHODIST_all.csv` in a `data/` folder, then run the notebook from top to bottom.
