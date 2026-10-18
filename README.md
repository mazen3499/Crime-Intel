# 🛡️ Crime Intel: Law Enforcement Analytics & BI Command Center

![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?logo=pandas&logoColor=white)
![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-F7931E?logo=scikitlearn&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures-0078D4)
![Dark Mode UI](https://img.shields.io/badge/UI-Dark%20Mode-0B1220)

A cleaned crime incident dataset and a five-page Power BI report for exploring where, when and how incidents were recorded.

![Executive Overview](dashboard/images/1_Executive_Overview.png)

The report has five pages: Executive Overview, Hotspots & Timing, Suspect & Victim Profiles, Trends & Seasonality, and Financial Impact & Case Outcomes.

[Overview](#-project-overview) · [KPIs](#-executive-kpi-highlights) · [Workflow](#-project-architecture--workflow) · [Insights](#-key-business--operational-insights) · [Showcase](#-dashboard-showcase-pages-2-to-5) · [Structure](#-project-structure) · [Run it](#-run-it-yourself) · [Data notes](#-data-notes-and-limitations)

## 📌 Project Overview

Crime Intel takes a raw crime incident file of 5,250 rows and turns it into an analysis-ready dataset of 5,050 incidents across 10 districts, from 2018 to 2024. A Python notebook removes duplicates, resolves messy labels with fuzzy matching, fills missing values with flagged group-based rules, and adds derived fields, including a rule-based felony or misdemeanor label and an exploratory K-Means segmentation.

A five-page Power BI report sits on top of that dataset. It is laid out as a command center for a patrol-planning team: seven KPIs on every page, year and case status filters, and separate pages for hotspots and timing, suspect and victim profiles, trends, and financial outcomes. The data looks synthetic (see the data notes at the end), so the findings demonstrate the workflow and do not describe a real city.

## ⚡ Executive KPI Highlights

| Metric | Value | Context |
|---|---|---|
| Total incidents | **5,050** | 200 duplicate IDs removed from 5,250 raw rows |
| Total property loss | **$125.69M** | $112.10M recorded plus $13.58M estimated for missing losses |
| Average loss per incident | **$24.89K** | $24.91K on recorded losses only |
| Open or active cases | **65.3%** | Open, pending or under investigation |
| Total arrests | **11,731** | Filled arrest counts are 0, so the total comes from recorded values |
| Average suspect age | **45.59** | 45.43 on recorded ages only |
| Felony rate | **72.2%** | Rule-based heuristic, not a legal classification |

## 🔄 Project Architecture & Workflow

1. **Data cleaning and standardization (silver layer)**
   - 200 duplicate incident IDs removed, which takes the file from 5,250 to 5,050 rows.
   - Fuzzy matching with `thefuzz` (Levenshtein distance) resolves 182 raw crime type spellings and 131 district spellings into 36 crime type labels and 10 districts. Five crime type aliases were missed and are listed in the data notes.
   - Missing values are filled by group: the most common value within crime type and district for gender, weapon and case status (falling back to crime type), the district mode for severity, and medians for ages and losses. Eleven `*_imputed` columns record every filled value.
   - Coordinates outside latitude 24 to 49 and longitude -125 to -67 are set to missing. Negative arrest counts and losses are converted to absolute values.

2. **Feature engineering and ML segmentation (gold layer)**
   - A rule-based felony or misdemeanor label: fixed rules by crime type, a severity rule for assault-type offenses, and a $2,500 loss threshold for theft and fraud types.
   - Exploratory K-Means clustering with k = 4 on scaled latitude, longitude, hour and property loss. The silhouette score is 0.195, so the segments are weakly separated.

3. **Data modeling and BI engineering (Power BI)**
   - An incident fact table with a dedicated Date table, marked as the date table. Measures include year-over-year change (`SAMEPERIODLASTYEAR`) and a `TREATAS` comparison of suspect and victim gender.
   - Three header cards on every page (a filter summary and two page-specific facts), driven by DAX measures and the year and case status slicers.

## 📊 Key Business & Operational Insights

- **02:00 is the busiest hour** with 252 incidents, 24% above the 203 hourly average. 09:00 follows closely with 249. **Thursday** is the busiest weekday on the dashboard (735 incidents with a recorded time of day). No single weekday and hour cell dominates: the highest has 45 incidents.
- **North** records **696** incidents, the most of any district, and the three busiest districts hold **37%** of all incidents.
- **Assault** is the most frequent label as exported (**352** incidents), ahead of Trespassing (323) and Drug Offense (290).
- **Yearly volume stayed between 683 and 768 incidents.** The largest rise was +8.5% in 2024 and the largest drop was -5.6% in 2023. These changes are not statistically significant (chi-square test, p = 0.27).
- **Average recorded loss is $24.5K to $26.4K** across the eight most frequent crime types, so crime type does not separate financial loss in this data.
- **Firearms account for 37%** of incidents with a recorded weapon (1,539 of 4,120).
- **Victim ages are spread evenly**, 501 to 551 per age group. The 80+ group is the largest only because it covers 11 ages (80 to 90) while the others cover 10.

## 💡 Strategic Law Enforcement Recommendations

- **Plan shift coverage by hour rather than by weekday.** 02:00 and 09:00 run 24% and 23% above the hourly average, while weekday differences are small.
- **Compare investigative workload in North and South**, which together record 27% of incidents (1,364 of 5,050). The report has no clearance rate by district, so that measure is the natural next addition.
- **Track the 65.3% open or active share** by year and crime type to see where cases stall.
- **Repeat the analysis on verified data** before any staffing decision. The dataset looks synthetic and most differences are small.

## 🛠️ Tech Stack

| Area | Tools |
|---|---|
| Analysis | Python 3.12, Pandas, NumPy, Scikit-Learn, TheFuzz, Matplotlib, Seaborn, Jupyter Notebook |
| Statistics | SciPy (chi-square checks) |
| Business intelligence | Power BI Desktop, Power Query, DAX |
| Version control | Git & GitHub |

## 🚀 Dashboard Showcase (Pages 2 to 5)

### 2. Hotspots & Timing

![Hotspots & Timing](dashboard/images/2_Hotspots_Timing.png)

A weekday by hour heatmap, incidents by weekday and by hour, and incidents by state. The weekday and hour charts count the **4,877** incidents that have a recorded time of day.

### 3. Suspect & Victim Profiles

![Suspect & Victim Profiles](dashboard/images/3_Suspect_Victim_Profiles.png)

Age distributions for suspects and victims (recorded ages), a gender comparison and the race breakdown of identified suspects. The two peak-age header cards show the median fill values, 46 and 50 (see the data notes).

### 4. Trends & Seasonality

![Trends & Seasonality](dashboard/images/4_Trends_Seasonality.png)

Year-over-year change, incidents per year, a month by year heatmap and the five most frequent crime types over time. The largest yearly rise is **+8.5%** (2024) and the largest drop is **-5.6%** (2023).

### 5. Financial Impact & Case Outcomes

![Financial Impact & Case Outcomes](dashboard/images/5_Financial_Outcomes.png)

Average recorded loss by crime type, weapon use, case resolution and case status, all on recorded values. Firearms lead at **37%** of incidents with a recorded weapon. The "Most used weapon" header card differs from the grouped chart (see the data notes).

## 📁 Project Structure

```text
Crime-Intel/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── Crime_Data_after_cleaning.ipynb
└── dashboard/
    ├── Crime_Intel.pbix
    └── images/
        ├── 1_Executive_Overview.png
        ├── 2_Hotspots_Timing.png
        ├── 3_Suspect_Victim_Profiles.png
        ├── 4_Trends_Seasonality.png
        └── 5_Financial_Outcomes.png
# 🛡️ Crime Intel: Law Enforcement Analytics & BI Command Center

![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?logo=pandas&logoColor=white)
![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-F7931E?logo=scikitlearn&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures-0078D4)
![Dark Mode UI](https://img.shields.io/badge/UI-Dark%20Mode-0B1220)

A cleaned crime incident dataset and a five-page Power BI report for exploring where, when and how incidents were recorded.

![Executive Overview](dashboard/images/1_Executive_Overview.png)

The report has five pages: Executive Overview, Hotspots & Timing, Suspect & Victim Profiles, Trends & Seasonality, and Financial Impact & Case Outcomes.

[Overview](#-project-overview) · [KPIs](#-executive-kpi-highlights) · [Workflow](#-project-architecture--workflow) · [Insights](#-key-business--operational-insights) · [Showcase](#-dashboard-showcase-pages-2-to-5) · [Structure](#-project-structure) · [Run it](#-run-it-yourself) · [Data notes](#-data-notes-and-limitations)

## 📌 Project Overview

Crime Intel takes a raw crime incident file of 5,250 rows and turns it into an analysis-ready dataset of 5,050 incidents across 10 districts, from 2018 to 2024. A Python notebook removes duplicates, resolves messy labels with fuzzy matching, fills missing values with flagged group-based rules, and adds derived fields, including a rule-based felony or misdemeanor label and an exploratory K-Means segmentation.

A five-page Power BI report sits on top of that dataset. It is laid out as a command center for a patrol-planning team: seven KPIs on every page, year and case status filters, and separate pages for hotspots and timing, suspect and victim profiles, trends, and financial outcomes. The data looks synthetic (see the data notes at the end), so the findings demonstrate the workflow and do not describe a real city.

## ⚡ Executive KPI Highlights

| Metric | Value | Context |
|---|---|---|
| Total incidents | **5,050** | 200 duplicate IDs removed from 5,250 raw rows |
| Total property loss | **$125.69M** | $112.10M recorded plus $13.58M estimated for missing losses |
| Average loss per incident | **$24.89K** | $24.91K on recorded losses only |
| Open or active cases | **65.3%** | Open, pending or under investigation |
| Total arrests | **11,731** | Filled arrest counts are 0, so the total comes from recorded values |
| Average suspect age | **45.59** | 45.43 on recorded ages only |
| Felony rate | **72.2%** | Rule-based heuristic, not a legal classification |

## 🔄 Project Architecture & Workflow

1. **Data cleaning and standardization (silver layer)**
   - 200 duplicate incident IDs removed, which takes the file from 5,250 to 5,050 rows.
   - Fuzzy matching with `thefuzz` (Levenshtein distance) resolves 182 raw crime type spellings and 131 district spellings into 36 crime type labels and 10 districts. Five crime type aliases were missed and are listed in the data notes.
   - Missing values are filled by group: the most common value within crime type and district for gender, weapon and case status (falling back to crime type), the district mode for severity, and medians for ages and losses. Eleven `*_imputed` columns record every filled value.
   - Coordinates outside latitude 24 to 49 and longitude -125 to -67 are set to missing. Negative arrest counts and losses are converted to absolute values.

2. **Feature engineering and ML segmentation (gold layer)**
   - A rule-based felony or misdemeanor label: fixed rules by crime type, a severity rule for assault-type offenses, and a $2,500 loss threshold for theft and fraud types.
   - Exploratory K-Means clustering with k = 4 on scaled latitude, longitude, hour and property loss. The silhouette score is 0.195, so the segments are weakly separated.

3. **Data modeling and BI engineering (Power BI)**
   - An incident fact table with a dedicated Date table, marked as the date table. Measures include year-over-year change (`SAMEPERIODLASTYEAR`) and a `TREATAS` comparison of suspect and victim gender.
   - Three header cards on every page (a filter summary and two page-specific facts), driven by DAX measures and the year and case status slicers.

## 📊 Key Business & Operational Insights

- **02:00 is the busiest hour** with 252 incidents, 24% above the 203 hourly average. 09:00 follows closely with 249. **Thursday** is the busiest weekday on the dashboard (735 incidents with a recorded time of day). No single weekday and hour cell dominates: the highest has 45 incidents.
- **North** records **696** incidents, the most of any district, and the three busiest districts hold **37%** of all incidents.
- **Assault** is the most frequent label as exported (**352** incidents), ahead of Trespassing (323) and Drug Offense (290).
- **Yearly volume stayed between 683 and 768 incidents.** The largest rise was +8.5% in 2024 and the largest drop was -5.6% in 2023. These changes are not statistically significant (chi-square test, p = 0.27).
- **Average recorded loss is $24.5K to $26.4K** across the eight most frequent crime types, so crime type does not separate financial loss in this data.
- **Firearms account for 37%** of incidents with a recorded weapon (1,539 of 4,120).
- **Victim ages are spread evenly**, 501 to 551 per age group. The 80+ group is the largest only because it covers 11 ages (80 to 90) while the others cover 10.

## 💡 Strategic Law Enforcement Recommendations

- **Plan shift coverage by hour rather than by weekday.** 02:00 and 09:00 run 24% and 23% above the hourly average, while weekday differences are small.
- **Compare investigative workload in North and South**, which together record 27% of incidents (1,364 of 5,050). The report has no clearance rate by district, so that measure is the natural next addition.
- **Track the 65.3% open or active share** by year and crime type to see where cases stall.
- **Repeat the analysis on verified data** before any staffing decision. The dataset looks synthetic and most differences are small.

## 🛠️ Tech Stack

| Area | Tools |
|---|---|
| Analysis | Python 3.12, Pandas, NumPy, Scikit-Learn, TheFuzz, Matplotlib, Seaborn, Jupyter Notebook |
| Statistics | SciPy (chi-square checks) |
| Business intelligence | Power BI Desktop, Power Query, DAX |
| Version control | Git & GitHub |

## 🚀 Dashboard Showcase (Pages 2 to 5)

### 2. Hotspots & Timing

![Hotspots & Timing](dashboard/images/2_Hotspots_Timing.png)

A weekday by hour heatmap, incidents by weekday and by hour, and incidents by state. The weekday and hour charts count the **4,877** incidents that have a recorded time of day.

### 3. Suspect & Victim Profiles

![Suspect & Victim Profiles](dashboard/images/3_Suspect_Victim_Profiles.png)

Age distributions for suspects and victims (recorded ages), a gender comparison and the race breakdown of identified suspects. The two peak-age header cards show the median fill values, 46 and 50 (see the data notes).

### 4. Trends & Seasonality

![Trends & Seasonality](dashboard/images/4_Trends_Seasonality.png)

Year-over-year change, incidents per year, a month by year heatmap and the five most frequent crime types over time. The largest yearly rise is **+8.5%** (2024) and the largest drop is **-5.6%** (2023).

### 5. Financial Impact & Case Outcomes

![Financial Impact & Case Outcomes](dashboard/images/5_Financial_Outcomes.png)

Average recorded loss by crime type, weapon use, case resolution and case status, all on recorded values. Firearms lead at **37%** of incidents with a recorded weapon. The "Most used weapon" header card differs from the grouped chart (see the data notes).

## 📁 Project Structure

```text
Crime-Intel/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── Crime_Data_after_cleaning.ipynb
└── dashboard/
    ├── Crime_Intel.pbix
    └── images/
        ├── 1_Executive_Overview.png
        ├── 2_Hotspots_Timing.png
        ├── 3_Suspect_Victim_Profiles.png
        ├── 4_Trends_Seasonality.png
        └── 5_Financial_Outcomes.png
# 🛡️ Crime Intel: Law Enforcement Analytics & BI Command Center

![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?logo=pandas&logoColor=white)
![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-F7931E?logo=scikitlearn&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures-0078D4)
![Dark Mode UI](https://img.shields.io/badge/UI-Dark%20Mode-0B1220)

A cleaned crime incident dataset and a five-page Power BI report for exploring where, when and how incidents were recorded.

![Executive Overview](dashboard/images/1_Executive_Overview.png)

The report has five pages: Executive Overview, Hotspots & Timing, Suspect & Victim Profiles, Trends & Seasonality, and Financial Impact & Case Outcomes.

[Overview](#-project-overview) · [KPIs](#-executive-kpi-highlights) · [Workflow](#-project-architecture--workflow) · [Insights](#-key-business--operational-insights) · [Showcase](#-dashboard-showcase-pages-2-to-5) · [Structure](#-project-structure) · [Run it](#-run-it-yourself) · [Data notes](#-data-notes-and-limitations)

## 📌 Project Overview

Crime Intel takes a raw crime incident file of 5,250 rows and turns it into an analysis-ready dataset of 5,050 incidents across 10 districts, from 2018 to 2024. A Python notebook removes duplicates, resolves messy labels with fuzzy matching, fills missing values with flagged group-based rules, and adds derived fields, including a rule-based felony or misdemeanor label and an exploratory K-Means segmentation.

A five-page Power BI report sits on top of that dataset. It is laid out as a command center for a patrol-planning team: seven KPIs on every page, year and case status filters, and separate pages for hotspots and timing, suspect and victim profiles, trends, and financial outcomes. The data looks synthetic (see the data notes at the end), so the findings demonstrate the workflow and do not describe a real city.

## ⚡ Executive KPI Highlights

| Metric | Value | Context |
|---|---|---|
| Total incidents | **5,050** | 200 duplicate IDs removed from 5,250 raw rows |
| Total property loss | **$125.69M** | $112.10M recorded plus $13.58M estimated for missing losses |
| Average loss per incident | **$24.89K** | $24.91K on recorded losses only |
| Open or active cases | **65.3%** | Open, pending or under investigation |
| Total arrests | **11,731** | Filled arrest counts are 0, so the total comes from recorded values |
| Average suspect age | **45.59** | 45.43 on recorded ages only |
| Felony rate | **72.2%** | Rule-based heuristic, not a legal classification |

## 🔄 Project Architecture & Workflow

1. **Data cleaning and standardization (silver layer)**
   - 200 duplicate incident IDs removed, which takes the file from 5,250 to 5,050 rows.
   - Fuzzy matching with `thefuzz` (Levenshtein distance) resolves 182 raw crime type spellings and 131 district spellings into 36 crime type labels and 10 districts. Five crime type aliases were missed and are listed in the data notes.
   - Missing values are filled by group: the most common value within crime type and district for gender, weapon and case status (falling back to crime type), the district mode for severity, and medians for ages and losses. Eleven `*_imputed` columns record every filled value.
   - Coordinates outside latitude 24 to 49 and longitude -125 to -67 are set to missing. Negative arrest counts and losses are converted to absolute values.

2. **Feature engineering and ML segmentation (gold layer)**
   - A rule-based felony or misdemeanor label: fixed rules by crime type, a severity rule for assault-type offenses, and a $2,500 loss threshold for theft and fraud types.
   - Exploratory K-Means clustering with k = 4 on scaled latitude, longitude, hour and property loss. The silhouette score is 0.195, so the segments are weakly separated.

3. **Data modeling and BI engineering (Power BI)**
   - An incident fact table with a dedicated Date table, marked as the date table. Measures include year-over-year change (`SAMEPERIODLASTYEAR`) and a `TREATAS` comparison of suspect and victim gender.
   - Three header cards on every page (a filter summary and two page-specific facts), driven by DAX measures and the year and case status slicers.

## 📊 Key Business & Operational Insights

- **02:00 is the busiest hour** with 252 incidents, 24% above the 203 hourly average. 09:00 follows closely with 249. **Thursday** is the busiest weekday on the dashboard (735 incidents with a recorded time of day). No single weekday and hour cell dominates: the highest has 45 incidents.
- **North** records **696** incidents, the most of any district, and the three busiest districts hold **37%** of all incidents.
- **Assault** is the most frequent label as exported (**352** incidents), ahead of Trespassing (323) and Drug Offense (290).
- **Yearly volume stayed between 683 and 768 incidents.** The largest rise was +8.5% in 2024 and the largest drop was -5.6% in 2023. These changes are not statistically significant (chi-square test, p = 0.27).
- **Average recorded loss is $24.5K to $26.4K** across the eight most frequent crime types, so crime type does not separate financial loss in this data.
- **Firearms account for 37%** of incidents with a recorded weapon (1,539 of 4,120).
- **Victim ages are spread evenly**, 501 to 551 per age group. The 80+ group is the largest only because it covers 11 ages (80 to 90) while the others cover 10.

## 💡 Strategic Law Enforcement Recommendations

- **Plan shift coverage by hour rather than by weekday.** 02:00 and 09:00 run 24% and 23% above the hourly average, while weekday differences are small.
- **Compare investigative workload in North and South**, which together record 27% of incidents (1,364 of 5,050). The report has no clearance rate by district, so that measure is the natural next addition.
- **Track the 65.3% open or active share** by year and crime type to see where cases stall.
- **Repeat the analysis on verified data** before any staffing decision. The dataset looks synthetic and most differences are small.

## 🛠️ Tech Stack

| Area | Tools |
|---|---|
| Analysis | Python 3.12, Pandas, NumPy, Scikit-Learn, TheFuzz, Matplotlib, Seaborn, Jupyter Notebook |
| Statistics | SciPy (chi-square checks) |
| Business intelligence | Power BI Desktop, Power Query, DAX |
| Version control | Git & GitHub |

## 🚀 Dashboard Showcase (Pages 2 to 5)

### 2. Hotspots & Timing

![Hotspots & Timing](dashboard/images/2_Hotspots_Timing.png)

A weekday by hour heatmap, incidents by weekday and by hour, and incidents by state. The weekday and hour charts count the **4,877** incidents that have a recorded time of day.

### 3. Suspect & Victim Profiles

![Suspect & Victim Profiles](dashboard/images/3_Suspect_Victim_Profiles.png)

Age distributions for suspects and victims (recorded ages), a gender comparison and the race breakdown of identified suspects. The two peak-age header cards show the median fill values, 46 and 50 (see the data notes).

### 4. Trends & Seasonality

![Trends & Seasonality](dashboard/images/4_Trends_Seasonality.png)

Year-over-year change, incidents per year, a month by year heatmap and the five most frequent crime types over time. The largest yearly rise is **+8.5%** (2024) and the largest drop is **-5.6%** (2023).

### 5. Financial Impact & Case Outcomes

![Financial Impact & Case Outcomes](dashboard/images/5_Financial_Outcomes.png)

Average recorded loss by crime type, weapon use, case resolution and case status, all on recorded values. Firearms lead at **37%** of incidents with a recorded weapon. The "Most used weapon" header card differs from the grouped chart (see the data notes).

## 📁 Project Structure

```text
Crime-Intel/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── Crime_Data_after_cleaning.ipynb
└── dashboard/
    ├── Crime_Intel.pbix
    └── images/
        ├── 1_Executive_Overview.png
        ├── 2_Hotspots_Timing.png
        ├── 3_Suspect_Victim_Profiles.png
        ├── 4_Trends_Seasonality.png
        └── 5_Financial_Outcomes.png
git clone [https://github.com/mazen3499/Crime-Intel.git](https://github.com/mazen3499/Crime-Intel.git)
cd Crime-Intel
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
Save the raw file as notebooks/crime_incidents_messy.csv. It is not in the repository (see the data notes).

Run cd notebooks && jupyter lab, open Crime_Data_after_cleaning.ipynb and run all cells. The notebook writes crime_incidents_cleaned.csv (5,050 rows, 53 columns) next to itself.

Open dashboard/Crime_Intel.pbix in Power BI Desktop on Windows. To refresh from your own copy, change the file path under Transform data, Data source settings. If the CSV source step contains Columns=, set it to 53 or remove it.

The notebook was run end to end on Python 3.12. requirements.txt has not been tested in a clean environment.

🔎 Data notes and limitations
Source and privacy. The source and license of the dataset are not documented, and the data looks synthetic: coordinates are spread almost evenly and do not follow the recorded state, and category counts are close to uniform. The raw file contains name, phone and address columns, so no data files are committed. Remove those columns from the Power BI model before publishing the report file.

Filled values. Eleven fields needed filling, between 6.4% and 32.8% of rows. Charts labeled "recorded values" exclude filled rows. Totals include estimates: property loss is $125.69M with fills and $112.10M on recorded values.

Unmerged aliases. Five crime type aliases (Dv, Drunk Driving, D.U.I., Dwi and B&E) were not merged by the fuzzy matching. Merged, DUI would reach 353 incidents, ahead of Assault at 352, and Domestic Violence would reach 320. Rankings here use the labels as exported.

Weekday and hour charts. They exclude 173 incidents whose timestamp is exactly 00:00:00, which leaves 4,877 of 5,050.

Header cards. "Peak suspect age" (46) and "Peak victim age" (50) equal the median fill values. "Most used weapon" (Blunt Object) differs from the Weapon Used chart, where Firearm is highest.

Cleaning assumptions. Negative losses and arrest counts are treated as sign errors and made positive. Timestamps missing in 329 rows were copied from the previous row. Neither assumption has been checked against a source.

Modeling. The K-Means silhouette is 0.195 at k = 4 and inertia shows no clear elbow, so the segments are exploratory. The felony label rests on crime type rules and a $2,500 threshold that were not checked against legal sources. The chi-square and silhouette checks were run outside the notebook.

Counts, not rates. The data has no population figures.

👤 Author
Mazen Walid

GitHub · LinkedIn
