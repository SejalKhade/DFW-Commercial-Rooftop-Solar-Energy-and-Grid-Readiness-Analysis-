# DFW Commercial Rooftop Solar Energy & Grid Readiness Analysis

End-to-end geospatial analytics pipeline assessing rooftop solar energy potential,
CO₂ reduction, installation costs, and EV charging infrastructure gaps for **8,612
large commercial and industrial buildings** across **11 DFW counties** — integrating
OSMnx, US Census ACS5, EPA eGRID, and NREL data into interactive Folium maps,
spatial regression models, and a Power BI dashboard.

[![Python](https://img.shields.io/badge/python-3.11-blue.svg)](https://www.python.org/)
[![GeoPandas](https://img.shields.io/badge/spatial-GeoPandas-green.svg)](https://geopandas.org/)
[![Power BI](https://img.shields.io/badge/dashboard-Power%20BI-yellow.svg)](https://powerbi.microsoft.com/)

---

## Key Results

| Metric | Value |
|---|---|
| Buildings analyzed | **8,612** large commercial & industrial (≥ 20,000 sq ft) |
| Counties covered | **11** DFW counties |
| Total annual energy potential | **15,706 GWh/year** (15.71M MWh/year) |
| CO₂ emissions avoided | **5.261 Million metric tons/year** |
| Total installation cost | **$19.56 Billion** (NREL benchmark $2.00/W) |
| Households powered (ERCOT) | **1,194,170** |
| Median building size | 42,365 sq ft |
| Median output per building | 822 MWh/year |
| Buildings with zero EV stations within 1 mi | **2,609 (30.3%)** |
| Buildings costing < $2M to install | **6,481 (75.3%)** |
| Dallas County share | **51.7%** of total generation (5,066 assets) |
| Top 4 counties (Dallas/Tarrant/Denton/Collin) | **94.9%** of total energy |
| Peak monthly output (July) | 1.570 Billion kWh |
| Trough monthly output (December) | 0.957 Billion kWh |

---

## Building Type Breakdown

| Building Type | Count | Share | Annual MWh/yr | CO₂ Avoided (M t/yr) |
|---|---|---|---|---|
| Commercial | 2,562 | 29.75% | 2,519,897 | 0.844 |
| Retail | 2,260 | 26.24% | 2,646,662 | 0.887 |
| Warehouse | 1,929 | 22.40% | 5,535,449 | 1.854 |
| Industrial | 1,283 | 14.90% | 4,490,801 | 1.504 |
| Office | 576 | 6.69% | 510,047 | 0.171 |
| Supermarket | 2 | 0.02% | 2,859 | 0.001 |

**Warehouse + Industrial = 37.3% of buildings but 63.84% of total energy (1.71× concentration)**

---

## Visualizations

### Building Type Analysis
![Building Type Analysis](Images/01_building_type.png)
*4-panel: building count, total annual energy (MWh/yr), CO₂ avoided, installation cost by building type. Warehouses and industrial lead energy output despite being fewer in number.*

### County-Level 4-Panel Analysis
![County Panels](Images/01b_county_panels.png)
*Same 4 metrics broken down across all 11 DFW counties. Dallas dominates at 51.7% of portfolio energy.*

### Building Type Share: Count vs Energy
![Building Type Pie](Images/02_building_type_pie.png)
*Paired pie charts: warehouse expands from 22.4% → 35.24% of energy; commercial contracts from 29.75% → 16.04%. Count-based targeting would systematically misallocate resources.*

### County Solar Analysis
![County Analysis](Images/03_county_analysis.png)
*County-level energy, building count, and installation cost. Collin–Denton rank reversal visible: Denton ranks 3rd by energy despite 4th by count.*

### Energy Concentration (Pareto Curve)
![Pareto Energy](Images/05_pareto_energy.png)
*Top 10.3% of buildings (889 assets) → 50% of energy. Top 40.4% (3,483 assets) → 80%. A first-wave programme targeting the top decile captures half the portfolio at 9.7× less scope.*

### Building Size Distribution
![Building Size Distribution](Images/06_building_size_dist.png)
*57.4% of buildings fall in the 20K–50K sq ft bracket; only 266 (3.1%) exceed 500K sq ft — these outliers drive the steep Pareto curve.*

### Carbon Emission Reduction
![Carbon Analysis](Images/07_carbon_analysis.png)
*CO₂ avoidance by building type and county using ERCOT factor 0.386 kg/kWh. Dallas County: 2.721M metric tons/year.*

### Installation Cost Analysis
![Cost Analysis](Images/08_cost_analysis.png)
*75.3% of buildings cost < $2M to install — within reach of PPAs and C-PACE financing. Only 49 buildings (0.57%) exceed $25M.*

### Household Proximity Scatter
![Household Scatter](Images/09_household_scatter.png)
*Pearson r = −0.193 (1 mi) and −0.227 (3 mi): the highest-generation buildings are systematically located in less residentially dense areas — the core equity-vs-efficiency planning tension.*

### Solar Potential Per Capita Map
![Per Capita Map](Images/09_percapita_map.png)
*Census-tract-level choropleth of MWh per household. Suburban and exurban tracts show outsized solar potential relative to population.*

### EV Charging Station Proximity
![EV Analysis](Images/10_ev_analysis.png)
*Distribution of EV stations within 1-mile buffer. 30.3% of buildings have zero stations — prime co-deployment targets.*

### EV Service Gap Map
![EV Service Gap Map](Images/11_ev_service_gap_map.png)
*Spatial distribution of EV infrastructure gaps. Red/orange = high solar potential with no EV stations nearby — highest-priority combined investment zones.*

### Pearson Correlation Matrix
![Pearson Correlation](Images/12_pearson_correlation.png)
*Correlation between building size, household density (1mi/3mi), EV station counts (1mi/3mi), latitude, and longitude.*

### Monthly Solar Output
![Seasonal Output](Images/14_seasonal_output.png)
*Monthly fleet output vs NREL peak sun hours. July peak = 1.570B kWh; December trough = 0.957B kWh. Peaks align with ERCOT summer demand — defers investment in peaking capacity.*

### Power BI Dashboard — Full Portfolio View
![Power BI Dashboard Full Portfolio](Images/dashboard_full.png)
*Full DFW portfolio overview: 8,612 buildings, 15.71M MWh/year, $19.56B install cost, 5.26M tons CO₂/year.*

### Power BI Dashboard — Filtered View
![Power BI Dashboard Filtered](Images/dashboard_filtered.png)
*Filtered segment view with Sankey chart showing building type to energy generation flow.*

### Interactive Map — Urban Dallas
![Map Urban Dallas](Images/map_urban_dallas.png)
*Interactive Folium map — inner Dallas cluster showing building type colour coding and marker size scaled to energy potential.*

### Interactive Map — Suburban Coverage
![Map Suburban](Images/map_suburban.png)
*Outer-county and suburban building distribution across the full 11-county study area.*

---

## Spatial Statistics (Sections 16–31)

The second half of the analysis moves from building-level to census-tract-level statistics.

- **OLS Regression** — solar potential vs household density per tract
- **Moran's I** — spatial autocorrelation test on OLS residuals (significant positive clustering confirmed, p < 0.05)
- **Spatial Error Model** (spreg/libpysal) — corrects for spatial autocorrelation, better fit than OLS alone
- **Bivariate choropleth** — census-tract map of solar potential per household × EV station presence

**4-way EV priority classification per census tract:**

| Category | Description |
|---|---|
| High solar / No station | Top priority — combined solar + EV investment |
| High solar / Station present | Solar-only opportunity |
| Low solar / No station | EV-only opportunity |
| Low solar / Station present | Lower priority |

---

## Technologies & Skills Used

| Category | Tools |
|---|---|
| **GIS & Geospatial** | OSMnx, GeoPandas, Shapely, EPSG:2276 reprojection, 1-mi & 3-mi spatial buffers, areal interpolation |
| **Interactive Mapping** | Folium (Leaflet.js), choropleth layers, multi-select buffer zone map, 27-category strategic filter map |
| **Data Engineering** | pandas, NumPy, US Census ACS5 API, NREL AFDC API, Nominatim geocoding (cached) |
| **Solar Modeling** | NREL NSRDB irradiance (H = 2007.5 kWh/m²/yr), PV equation E = A × r × H × PR, ERCOT 0.386 kg CO₂/kWh |
| **Spatial Statistics** | OLS (statsmodels), Moran's I (esda), Spatial Error Model (spreg), libpysal weights matrix |
| **Visualization** | Matplotlib, Seaborn, Power BI |
| **Environment** | Python 3.11, Google Colab, Jupyter Notebook |

---

## Repository Structure

```
notebooks/
  dfw_energy_analysis_final.ipynb        — complete pipeline (OSM → solar model →
                                           spatial stats → extraction zip)
  01_Asset_Data_Set.ipynb                — initial OSM asset pull
  02_Restarting_Energy_Project.ipynb
  03_CO2_cost_calculation.ipynb
  04_Forming_categories.ipynb
  DFW_Solar_Publication_Final.ipynb      — earlier publication draft

processed/
  final_solar_estimates.csv              — 8,612 buildings, all derived metrics
  DFW_Assets_Master.csv / .geojson       — enriched building dataset
  DFW_Assets_Enriched.csv
  EV_Stations_DFW.csv
  DFW_Census_Households.csv
  street_checkpoint.csv                  — Nominatim cache (saves ~2.5 hrs on re-run)

maps/
  DFW_Energy_Map.html                    — interactive strategic filter map (27 categories)
  DFW_Interactive_Tract_EV_Solar_Map.html — census-tract EV + solar bivariate map

Images/                                  — 15 analysis charts + Power BI screenshots

dashboard/
  Building_Asset_Mapping.pbix            — Power BI dashboard

category_reference/
  27_strategic_category_interpretations.xlsx
```

---

## Running the Notebook

Open `notebooks/dfw_energy_analysis_final.ipynb` in Google Colab or Jupyter and run all cells.

**Required API keys (both free):**
- Census API → https://api.census.gov/data/key_signup.html
- NREL API → https://developer.nrel.gov/signup/

Set as Colab Secrets (`CENSUS_API_KEY`, `NREL_API_KEY`) or environment variables.

> `processed/street_checkpoint.csv` caches 8,500/8,612 Nominatim lookups — only ~112 buildings need geocoding on a fresh run (~2 min vs ~2.5 hrs without it).

---

## Data Sources

| Source | Used for |
|---|---|
| OpenStreetMap via OSMnx | Building footprints and type classification |
| US Census Bureau ACS5 2024 | Household counts per tract and ZCTA |
| US Census TIGER 2025 | Tract and ZCTA boundary shapefiles |
| NREL Alternative Fuel Stations API | EV charging station locations |
| NREL NSRDB | Solar irradiance (H = 2007.5 kWh/m²/yr) |
| EPA eGRID (ERCOT region) | CO₂ emission factor (0.386 kg/kWh) |
| NREL cost benchmark | Installation cost ($2.00/W) |
