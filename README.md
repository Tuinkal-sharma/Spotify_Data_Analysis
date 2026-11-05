# Spotify Track Popularity Analysis (R)

![R](https://img.shields.io/badge/R-4.5-276DC3?logo=r&logoColor=white)
![ggplot2](https://img.shields.io/badge/ggplot2-Visualization-orange)
![caret](https://img.shields.io/badge/caret-Modeling-green)
![Model](https://img.shields.io/badge/Model-Linear%20Regression-blueviolet)

An end-to-end data science project in R that explores what drives a Spotify track's **popularity score (0 to 100)**. It covers data cleaning, hypothesis-driven exploratory analysis, and a linear regression model to predict track popularity.

---

## Project Files

| File | Description |
|------|-------------|
| [`spotify_datascience_project.pdf`](spotify_datascience_project.pdf) | Full project report: R code, outputs, plots and conclusions |
| [`spotify_data_clean_csv.xlsx`](spotify_data_clean_csv.xlsx) | Dataset of Spotify tracks (15 columns) |

---

## Dataset

About 8,500 Spotify tracks with 15 columns:

| Type | Columns |
|------|---------|
| Track | `track_id`, `track_name`, `track_number`, `track_popularity`, `explicit`, `track_duration_min` |
| Artist | `artist_name`, `artist_popularity`, `artist_followers`, `artist_genres` |
| Album | `album_id`, `album_name`, `album_release_date`, `album_total_tracks`, `album_type` |

**Target variable:** `track_popularity` (0 to 100)

---

## Workflow

### 1. Data cleaning practice
To practice real-world cleaning, missing values, an outlier and duplicates were deliberately injected into a copy of the data, then fixed:

- **Missing values:** 50 values in `track_popularity` and 30 in `track_duration_min` set to NA, then filled with the **median** (robust to outliers)
- **Outlier:** one track duration set to 1000 minutes, then handled with **IQR capping** (Q3 + 1.5 x IQR)
- **Duplicates:** 10 duplicate rows added, then removed with `distinct()`
- **Type conversion:** `explicit` and `album_type` converted to factors

### 2. Hypotheses

| # | Hypothesis | Method | Outcome |
|---|-----------|--------|---------|
| H1 | Artist popularity has a strong positive linear relationship with track popularity | Scatter plot + linear trend line | Supported visually |
| H2 | Track duration has a non-linear (inverted U) effect on popularity | Scatter plot + GAM smoothing | Supported visually: popularity peaks around 3.5 to 4 minutes |
| H3 | Explicit tracks differ in average popularity from non-explicit tracks | Linear regression coefficient | Explicit tracks score about 2.7 points higher, statistically significant |

### 3. Exploratory analysis
- Track popularity vs. artist popularity (scatter + linear fit)
- Track popularity vs. duration (scatter + GAM smooth)
- Track popularity by album type (boxplot): albums have the highest median popularity, compilations the lowest

### 4. Modeling
- **Features:** `artist_popularity`, `explicit`, `track_duration_min`, `album_type`
- **Split:** 80% train / 20% test using `caret::createDataPartition` (seed 42)
- **Model:** Linear Regression (`lm`), chosen as an interpretable baseline
- Factor levels of the test set are aligned with the training set to avoid prediction errors

---

## Results

### Model performance (test set)

| Metric | Value |
|--------|-------|
| RMSE | 21.19 |
| MAE | 15.91 |
| R-squared | 0.224 |

On average, predictions are off by about 16 popularity points.

### Key coefficients

| Feature | Estimate | Significance |
|---------|----------|--------------|
| `artist_popularity` | +0.53 per point | Highly significant (p < 2e-16), strongest predictor |
| `explicit` (TRUE) | +2.68 | Significant (p < 0.001) |
| `album_type` = compilation | -8.01 | Significant (p < 0.001) |
| `album_type` = single | -0.80 | Not significant |
| `track_duration_min` | +0.44 | Not significant (linear term) |

**Main takeaway:** An artist's popularity is by far the best predictor of how popular their tracks are. Each extra point of artist popularity adds roughly 0.53 points to track popularity.

---

## Limitations and Next Steps

- **Moderate fit:** R-squared of about 0.22 means the four features explain only a fifth of the variation in popularity
- **Non-linear duration effect:** the plot shows an inverted U, but linear regression cannot capture it. A **GAM**, polynomial term or tree-based model would fit better
- **Correlation check:** `cor()` returned `NA` because of missing values in the cleaned data. Using `use = "complete.obs"` fixes this
- **Hypotheses H1 to H3** were assessed mostly through plots. Formal tests (correlation test, t-test for explicit vs non-explicit) would strengthen the conclusions
- **Unused features:** genres, artist followers, release date and album size could improve the model
- **Skewed target:** many tracks have popularity near 0; a transformation or two-stage model may help

---

## How to Run

1. Install R and the required packages:
   ```r
   install.packages(c("readr", "readxl", "dplyr", "ggplot2", "tidyr", "caret", "e1071"))
   ```
2. Download `spotify_data_clean_csv.xlsx` from this repository
3. Load the data (the report uses a CSV; for the Excel file use):
   ```r
   library(readxl)
   df_original <- read_excel("spotify_data_clean_csv.xlsx")
   ```
   Or save the sheet as `spotify_data clean.csv` and use `read_csv()` as in the report
4. Run the code from `spotify_datascience_project.pdf` step by step

---

## Tech Stack

- **Language:** R
- **Data handling:** readr, dplyr, tidyr
- **Visualization:** ggplot2
- **Modeling:** caret, e1071

---

## Author

**Tuinkal Sharma**
B.Sc. Computer Science (2026), Dayalbagh Educational Institute, Agra
Aspiring Data Analyst | Interested in Data Analytics and Machine Learning
