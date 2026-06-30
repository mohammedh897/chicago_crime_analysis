# Chicago Crime Analysis (2001–2022)
### Project 3 — Part 1 | Time Series & Exploratory Data Analysis

---

## Project Overview

This project performs a comprehensive Exploratory Data Analysis (EDA) and Time Series Analysis on the Chicago Crime Dataset (2001–2022), covering over **7.7 million crime records** reported across the city of Chicago.

The analysis answers real-world stakeholder questions for a local newspaper reporter, covering crime trends, geographic distribution, temporal patterns, and seasonal decomposition.

---

## Dataset

| Property | Detail |
|---|---|
| Source | [Chicago Data Portal — Crimes 2001 to Present](https://data.cityofchicago.org/Public-Safety/Crimes-2001-to-Present/ijzp-q8t2) |
| Format | 22 CSV files (one per year), packaged in a ZIP archive |
| Total Records | 7,713,109 rows |
| Date Range | January 1, 2001 — December 31, 2022 |
| Key Columns | `Date`, `Primary Type`, `Description`, `Location Description`, `Arrest`, `Domestic`, `District`, `Ward`, `Latitude`, `Longitude` |

---

## Project Structure

```
chicago_crime_analysis.ipynb   <- Main analysis notebook (all topics)
README.md                      <- Project documentation
```

---

## Libraries Used

```
pandas          Data loading, cleaning, groupby, resampling
numpy           Numerical operations and trend slopes
matplotlib      Charts and visualizations
seaborn         Statistical plots with styling
statsmodels     Seasonal decomposition (seasonal_decompose)
holidays        US/Illinois holiday calendar
gdown           Google Drive zip download
zipfile / os    ZIP extraction and file handling
```

---

## Workflow Summary

**Step 0 — Import Libraries**
All required libraries imported; seaborn theme and matplotlib rcParams configured.

**Step 1 — Load Data from Google Drive**
ZIP file downloaded directly from Google Drive using `gdown`, then all 22 CSV files read in-memory from the archive using `zipfile` and concatenated with `pd.concat`.

**Step 2 — Clean & Prepare Data**
- Parsed `Date` column to datetime with `errors='coerce'`; dropped unparseable rows (0 found)
- Set `Date` as index and sorted chronologically
- Engineered features: `Year`, `Month`, `Hour`, `date_only`
- Built two data forms:
  - **Form 1:** Individual crime rows with datetime index (each row = 1 crime)
  - **Form 2:** Daily crime counts via `.resample('D').size()`

---

## Topics Analyzed

### Topic 1 — Comparing Police Districts (2022)

**Questions answered:**
- Which district had the most crimes in 2022?
- Which had the least?

**Key Findings:**

| | District | Crime Count |
|---|---|---|
| Most | District 8 | 14,805 crimes |
| Least | District 31 | 15 crimes |

There is a significant geographic disparity in crime distribution across Chicago's active police districts.

---

### Topic 2 — Crimes Across the Years

**Questions answered:**
- Is the total number of crimes increasing or decreasing?
- Are there individual crime types moving in the opposite direction?

**Key Findings:**
- Overall trend: **DECREASING** — from ~486,000 crimes (2001) to ~239,000 (2022), nearly a 50% reduction
- Linear regression confirms a slope of approximately -14,600 crimes/year

Crime types with an **increasing** trend while overall crime decreases:

| Crime Type | Pattern |
|---|---|
| Weapons Violation | Sharp increase, especially post-2015 |
| Deceptive Practice | Steady upward trend |
| Criminal Sexual Assault | Gradual rise |
| Stalking | Emerging upward trend |
| Concealed Carry License Violation | Sharp recent increase |

---

### Topic 3 — AM vs PM Rush Hour

**Time windows:**
- AM Rush: 7:00 AM — 9:59 AM
- PM Rush: 4:00 PM — 6:59 PM

**Questions answered:**
- Are crimes more common during AM or PM rush hour?
- What are the top 5 crimes during each period?
- Are Motor Vehicle Thefts more common in AM or PM?

**Key Findings:**

| Period | Total Crimes |
|---|---|
| AM Rush | 770,651 |
| PM Rush | 1,206,353 (more common) |

PM Rush has approximately **57% more crimes** than AM Rush.

Top 5 crime types by period:

| Rank | AM Rush | PM Rush |
|---|---|---|
| 1 | Theft | Theft |
| 2 | Battery | Battery |
| 3 | Criminal Damage | Criminal Damage |
| 4 | Burglary | Narcotics |
| 5 | Other Offense | Assault |

Motor Vehicle Theft:
- AM Rush: 41,578
- PM Rush: 53,716 — **more common in PM Rush**

---

### Topic 4 — Comparing Months

**Questions answered:**
- What months have the most/least crime?
- Are there crime types that do NOT follow the overall seasonal pattern?

**Key Findings:**

| | Month | Crime Count |
|---|---|---|
| Most | July | ~717,000 |
| Least | February | ~529,000 |

Overall pattern: crime peaks in summer and dips in winter.

Crime types that **peak in winter** (counter-seasonal, winter/summer ratio >= 0.85):
- Offense Involving Children
- Obscenity
- Deceptive Practice
- Narcotics
- Criminal Trespass
- Other Offense
- Prostitution
- Motor Vehicle Theft
- Kidnapping

Notably, **Motor Vehicle Theft** appears as both a counter-seasonal crime (winter-leaning monthly pattern) and a PM Rush peak crime, and shows a sharp recent surge in the seasonality analysis — making it a recurring point of concern across multiple topics.

---

### Topic 5 — Comparing Holidays

**Questions answered:**
- What are the top 3 holidays with the most crimes?
- For each of those 3, what are the top 5 most common crimes?

**Method:** Used the `holidays` Python package with `holidays.US(state='IL')` for all years 2001–2022, mapped to the `date_only` column.

**Top 3 Holidays by Crime Count:**

1. New Year's Day
2. Independence Day
3. Labor Day

**Top 5 Crimes on Each:**

Theft ranks #1 on New Year's Day, but **Battery** ranks #1 on both Independence Day and Labor Day (Theft is #2 on both). Criminal Damage is consistently #3 across all three. Independence Day and Labor Day both see Assault and Narcotics in their top 5, unlike New Year's Day.

---

### Topic 6 — Seasonality & Cycles

**Method:** `statsmodels.tsa.seasonal.seasonal_decompose()` applied with an additive model at weekly (period=52) and monthly (period=12) resampling frequencies for both total crimes and Motor Vehicle Theft.

**All Crimes (Weekly Resampled):**

| Property | Value |
|---|---|
| Cycle Length | ~52 weeks (~364 days, annual) |
| Seasonal Magnitude | ~1,600 crimes/week (min-to-max swing) |
| Trend | Steady decline through ~2015, then stabilizing |
| Residuals | Random noise around zero (no structure) |

**Motor Vehicle Theft (Weekly):**

| Property | Value |
|---|---|
| Cycle Length | Annual (52 weeks) |
| Seasonal Magnitude | ~55 thefts/week |
| Trend | Long-term decline until ~2015, followed by a sharp increase post-2020 |

**Key Insight:** Chicago crime follows a strong annual seasonal cycle with a clear summer peak. The long-run trend shows significant decline over two decades, but Motor Vehicle Theft is a notable exception — it appears as a counter-seasonal type, peaks in PM Rush, and shows a sharp recent surge, making it the single most concerning crime type in this dataset.

---

## Summary Table

| Topic | Key Finding |
|---|---|
| Districts | District 8 had the most crimes (14,805); District 31 the least (15) in 2022 |
| Yearly Trend | Overall crime nearly halved from ~486K (2001) to ~239K (2022) |
| Rush Hour | PM Rush has 57% more crimes than AM Rush; Motor Vehicle Theft also peaks in PM |
| Monthly | July peaks; February lowest — winter-leaning types include Narcotics, Deceptive Practice, and Motor Vehicle Theft |
| Holidays | New Year's Day, Independence Day, Labor Day are the top 3 — Theft tops New Year's Day, Battery tops the other two |
| Seasonality | Strong annual cycle (~1,600 weekly swing); Motor Vehicle Theft shows a concerning recent surge across all analyses |

---

## How to Run

1. Upload the ZIP file to Google Drive and set sharing to **"Anyone with the link"**
2. Copy your file ID from the share link and paste it into the `GDRIVE_LINK` variable in the notebook
3. Install dependencies:
```bash
pip install pandas numpy matplotlib seaborn statsmodels holidays gdown
```
4. Open `chicago_crime_analysis.ipynb` and run all cells top to bottom

---

## Author

**Mohammed Husein**
Computer Engineering Graduate | Data Science & ML Bootcamp — Axsos Academy
[GitHub](https://github.com/mohammedh897) | [LinkedIn](https://linkedin.com/in/mohd-husein/)
