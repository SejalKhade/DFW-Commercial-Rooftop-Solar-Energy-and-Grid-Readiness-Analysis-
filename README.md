# DFW Commercial Rooftop Solar Energy & Grid Readiness Analysis

End-to-end geospatial analytics pipeline assessing rooftop solar energy potential, CO₂ reduction, and installation costs for **8,612 large commercial and industrial buildings** across **11 DFW counties** — integrating OSMnx, US Census ACS, EPA eGRID, and NREL benchmark data into an interactive Folium map and Power BI dashboard.

[![Python](https://img.shields.io/badge/python-3.11-blue.svg)](https://www.python.org/)
[![GeoPandas](https://img.shields.io/badge/spatial-GeoPandas-green.svg)](https://geopandas.org/)
[![Power BI](https://img.shields.io/badge/dashboard-Power%20BI-yellow.svg)](https://powerbi.microsoft.com/)

---

## Project Outcomes

| Metric | Value |
|---|---|
| Buildings analyzed | **8,612** large commercial & industrial (≥20,000 sq ft) |
| Counties covered | **11** DFW counties |
| Total usable roof area | **48.90M sq meters** |
| Total annual energy potential | **15.71M MWh/year** |
| Total CO₂ reduction | **5.26M metric tons/year** |
| Total installation cost | **$19.56 billion** |
| Named coverage | **100%** (zero manual entry via fallback geocoding pipeline) |
| High/Very High priority assets | **276 buildings (3.2%)** identified for first-wave deployment |
| Highest single asset | **112,712 MWh/year** (industrial, East Abram St, Tarrant County) |
| Dallas County share | **51.7%** of total generation from 5,066 assets |
| Stakeholder outputs | Interactive Folium HTML map + Power BI dashboard (.pbix) |

---

## Screenshots

### Power BI Dashboard — Full Portfolio View
![Power BI Dashboard Full Portfolio](docs/images/dashboard_full.png)
*Full DFW portfolio: 8,612 buildings, 15.71M MWh/year, $19.56B install cost, 5.26M tons CO₂ reduction/year. Dallas leads with 5,066 assets generating 8.12M MWh/year.*

### Power BI Dashboard — Filtered View (Office Buildings)
![Power BI Dashboard Filtered](docs/images/dashboard_filtered.png)
*Office building segment: 2,260 assets, 2.65M MWh/year, 886.63K tons CO₂, $3.30B cost. Sankey chart shows building type to energy generation flow.*

### Interactive Folium Map — Urban Dallas (Medium Generation, High Household, High EV)
![Folium Map Urban Dallas](docs/images/map_urban_dallas.png)
*57 assets matching Medium generation + High household density + High EV infrastructure. Strategic interpretation: "Above average solar potential in densely populated areas with established EV infrastructure."*

### Interactive Folium Map — Suburban/Highway Corridor (Medium Generation, Low Household, High EV)
![Folium Map Suburban](docs/images/map_suburban.png)
*32 assets matching Medium generation + Low household density + High EV infrastructure. Strategic interpretation: "Likely highway rest stops or suburban retail centers. Solar panels can help offset the cost of running these public charging stations."*

---

## Solar Model Parameters

| Parameter | Value | Source |
|---|---|---|
| Peak sun hours/day | **5.6 hr** | NREL NSRDB (DFW) |
| Panel efficiency (r) | **0.19** | NREL 2024 |
| Performance ratio (PR) | **0.78** | Marion et al., 2005 |
| Usable roof fraction | **0.80** | Denholm et al., 2009 |
| CO₂ emission factor | **0.335 kg CO₂/kWh** | EPA eGRID ERCOT |
| Install cost benchmark | **$2.00/Wdc** | NREL 2025 commercial rooftop |

**Generation formula:** `E = A × r × H × PR`
where A = usable roof area (m²), r = 0.19, H = 2,044 kWh/m²/year (5.6 × 365), PR = 0.78

---

## Architecture

```
Stage 1 — Building Extraction (01_Asset_Data_Set.ipynb)
  OSMnx → filter ≥20,000 sq ft commercial, industrial, retail,
           office, warehouse, supermarket across 11 DFW counties
  Project to EPSG:2276 (Texas North Central) for accurate area math
  Output: DFW_Metroplex_Commercial_Assets_20k.csv (8,612 buildings)

Stage 2 — Asset Enrichment (02_Restarting_Energy_Project.ipynb)
  Nominatim reverse geocoding → 100% name coverage on 82.8% unnamed records
  Fallback: rule-based label generator [Building Type] on [Street Name]
  Census ACS household counts at tract level (1-mile + 3-mile buffers)
  EV charging station proximity (NREL Alt Fuels Station Locator API)
  Output: DFW_Assets_Enriched.csv, DFW_Assets_Master.csv/.geojson

Stage 3 — Solar Model (03_CO2_cost_calculation.ipynb)
  Physics-based generation: E = A × r × H × PR
  CO₂ reduction = Annual kWh × 0.335 kg CO₂/kWh (EPA eGRID ERCOT)
  System capacity (MW) = Usable area (m²) × r / 1000
  Install cost = Capacity (Wdc) × $2.00 (NREL 2025 benchmark)
  Output: final_solar_estimates.csv

Stage 4 — Priority Framework (04_Forming_categories.ipynb)
  Priority Score = 0.60 × norm(Annual_MWh) + 0.40 × norm(HH_1mi)
  Box-plot tiering (Low/Medium/High) across 3 independent dimensions:
    → Annual solar generation potential
    → Residential household density (1-mile Census buffer)
    → EV charging infrastructure proximity
  Result: 27-category strategic matrix → 276 High/Very High priority assets
  Output: Processed_DFW_Assets_Master.csv

Stage 5 — Stakeholder Deliverables
  Folium choropleth map (color-coded by tier, pop-up asset cards, searchable list)
  Power BI dashboard: Building Asset Mapping.pbix
    Tabs: Distribution by County | KPIs | Ribbon Chart | Generation Potential | Distribution by Building
    Filters: county · building type · generation tier · EV class
```

---

## Key Results

### Portfolio KPIs (from Power BI Dashboard)

| KPI | Value |
|---|---|
| Total usable area | 48.90M sq meters |
| Annual energy potential | 15.71M MWh/year |
| CO₂ reduction | 5.26M metric tons/year |
| Installation cost | $19.56 billion |

### Generation by County

| County | Assets | Annual Energy | Share |
|---|---|---|---|
| Dallas | 5,066 | 8.12M MWh | 51.7% |
| Tarrant | 1,498 | 3.40M MWh | 21.6% |
| Denton | 738 | 2.08M MWh | 13.2% |
| Collin | 1,003 | 1.30M MWh | 8.3% |
| Ellis | 86 | 0.30M MWh | 1.9% |
| Kaufman | 29 | 0.16M MWh | 1.0% |
| Johnson | 66 | 0.13M MWh | 0.8% |
| Rockwall | 57 | 0.09M MWh | 0.6% |
| Hunt | 28 | 0.07M MWh | 0.4% |
| Parker | 29 | 0.05M MWh | 0.3% |
| Wise | 12 | 0.01M MWh | 0.1% |

### Analysis Outputs (DFW_Solar_Publication_Final.ipynb)

The publication notebook produces 13 analysis sections:

| Section | Analysis | Key Finding |
|---|---|---|
| 3 | Portfolio KPIs | 8,612 buildings, 15.71M MWh/yr, $19.56B total cost |
| 4 | Building Type Analysis | Warehouse + Industrial = 63.8% of generation from 38.3% of assets |
| 4b | County-Level Metrics | Dallas County: 51.7% of generation, 58.8% of assets |
| 4c | Count vs Energy Share | Industrial buildings punch above weight — fewer buildings, higher output |
| 5 | County Analysis | Top 4 counties (Dallas, Tarrant, Denton, Collin) = 94.0% of total generation |
| 6 | Pareto Concentration Curve | Top 20% of buildings generate 65%+ of energy |
| 7 | Building Size Distribution | 20K–100K sq ft buildings are most numerous; >500K sq ft generate disproportionate energy |
| 8 | Carbon Reduction Analysis | 5.26M tons CO₂/year avoidable across portfolio |
| 9 | Installation Cost Analysis | Most assets fall in $1M–$5M cost bracket — accessible for commercial financing |
| 10 | Household Impact Scatter | Generation capacity and residential proximity are negatively correlated |
| 11 | EV Proximity Analysis | High EV density buildings concentrated in Dallas urban core |
| 13 | Pearson Correlation Analysis | Building size is primary driver; household density and EV proximity are independent |
| 15 | Monthly Solar Output | Peak June–August (6.3–6.5 peak sun hrs); trough December–January (3.9–4.2 hrs) |

---

## Key Technical Contributions

### 1. Naming Pipeline — 100% Coverage on 82.8% Unnamed Records

```python
# Two-stage geocoding fallback
# Primary: Nominatim reverse geocoding
result = geolocator.reverse((lat, lon), language='en')
name = result.raw.get('amenity') or result.raw.get('shop') or result.raw.get('office')

# Fallback: rule-based label
name = f"{building_type.title()} on {street_name}"
```

Result: 100% named coverage across 8,612 buildings with zero manual entry.

### 2. Physics-Based Solar Model — Fully Reproducible from Federal Benchmarks

```python
# Core generation formula: E = A × r × H × PR
usable_area_m2 = footprint_sqft * 0.0929 * 0.80      # 80% usable roof fraction
annual_kwh = usable_area_m2 * 0.19 * (5.6 * 365) * 0.78

# Business outputs per building
co2_reduction_tons = (annual_kwh * 0.335) / 1000       # EPA eGRID ERCOT factor
capacity_mw = usable_area_m2 * 0.19 / 1000
install_cost_usd = capacity_mw * 1_000_000 * 2.00      # NREL 2025 benchmark
households_powered = annual_kwh / 14_112               # EIA 2023 TX average
```

### 3. Composite Priority Score + 27-Category Matrix

```python
# Priority Score weights generation potential (60%) and residential proximity (40%)
Priority_Score = 0.60 × (Annual_MWh / max_MWh)
              + 0.40 × (Households_1mi / max_Households_1mi)

# Thresholds from box-plot statistics
Very_High  = Priority_Score >= 0.30   # first-wave deployment: 276 assets (3.2%)
High       = Priority_Score >= 0.20
Medium_Low = Priority_Score < 0.20

# 3 independent dimensions tiered separately → 27-category matrix (3³)
# Annual Generation:  Low / Medium / High
# Household Density:  Low / Medium / High
# EV Proximity:       Low / Medium / High
```

276 assets (3.2%) qualify as High or Very High — a deployable, prioritized shortlist for first-wave investment.

---

## Interactive Map Features

The Folium map (`DFW_Energy_Map_Filtered_With_Asset_List.html`) includes:

- **3 dropdown filters:** Annual Generation · Household Population · EV Infrastructure
- **Color-coded markers** by building type: Commercial (blue) · Warehouse (orange) · Industrial (purple) · Retail (green) · Office (dark blue) · Supermarket (red)
- **Marker size** proportional to energy potential
- **Pop-up asset cards** showing: building name, type, county, annual MWh, household tier, EV tier, EV station count
- **Searchable asset list** panel on the left
- **Strategic interpretation text** that updates per filter combination

---

## Tech Stack

| Layer | Tool |
|---|---|
| Spatial extraction | OSMnx |
| Geocoding | Nominatim (geopy) |
| Geometry & CRS | GeoPandas (EPSG:2276 → EPSG:4326) |
| Census data | US Census Bureau ACS API (B11001_001E) |
| EV stations | NREL Alternative Fuels Station Locator API |
| Solar & cost model | Python · Pandas · NumPy |
| Statistical tiering | Box-plot statistics (IQR, upper fence = Q3 + 1.5×IQR) |
| Interactive map | Folium (choropleth + pop-up cards + searchable list) |
| BI dashboard | Power BI (.pbix) — 5 tabs |
| Publication figures | Matplotlib · Seaborn · SciPy |

---

## Repository Structure

```
DFW-Commercial-Rooftop-Solar-Energy-and-Grid-Readiness-Analysis/
│
├── notebooks/
│   ├── 01_Asset_Data_Set.ipynb                    Stage 1 — OSMnx extraction
│   ├── 02_Restarting_Energy_Project.ipynb         Stage 2 — enrichment & geocoding
│   ├── 03_CO2_cost_calculation.ipynb              Stage 3 — solar model
│   ├── 04_Forming_categories.ipynb                Stage 4 — priority framework
│   └── DFW_Solar_Publication_Final.ipynb          Publication analysis (13 sections)
│
├── data/
│   ├── raw/
│   │   ├── DFW_Census_Households.csv
│   │   └── EV_Stations_DFW.csv
│   └── processed/
│       ├── DFW_Metroplex_Commercial_Assets_20k.csv
│       ├── DFW_Assets_Enriched.csv
│       ├── DFW_Assets_Master.csv
│       ├── DFW_Assets_Master.geojson
│       ├── final_solar_estimates.csv
│       └── Processed_DFW_Assets_Master.csv
│
├── outputs/
│   ├── maps/
│   │   └── DFW_Energy_Map_Filtered_With_Asset_List.html
│   └── dashboard/
│       └── Building_Asset_Mapping.pbix
│
├── docs/
│   ├── images/
│   │   ├── Figure1_Overview.png
│   │   ├── dashboard_full.png
│   │   ├── dashboard_filtered.png
│   │   ├── map_urban_dallas.png
│   │   └── map_suburban.png
│   └── DFW_Solar_Energy_Potential_Manuscript.docx
│
├── category_reference/
│   └── 27_strategic_category_interpretations.xlsx
│
├── requirements.txt
└── README.md
```

---

## Limitations

- Solar irradiance uses DFW annual average (5.6 hr/day) — seasonal variation modeled in Section 15 but not applied per-building
- Usable roof fraction (80%) is a literature estimate — actual area varies by HVAC, skylights, equipment
- EV proximity uses straight-line distance, not road network routing
- Census household density uses tract-level aggregation — sub-tract variation not captured
- Install cost uses 2025 NREL benchmark ($2.00/Wdc) — actual costs vary by contractor and site conditions
- CAPEX financing, grid interconnection costs, and permitting fees excluded from cost model

---

## Citation

Khade, S. (2026). *Rooftop Solar Energy Potential of Large Commercial and Industrial Buildings in the Dallas-Fort Worth Metroplex.* University of Texas at Arlington, M.S. Data Science.

---

## Author

**Sejal Khade**
MS Data Science · University of Texas at Arlington · May 2026
[GitHub](https://github.com/SejalKhade) · [LinkedIn](https://linkedin.com/in/sejallk) · [sejalkhade0023@gmail.com](mailto:sejalkhade0023@gmail.com)
