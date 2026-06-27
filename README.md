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
| Total generation potential | **18,230 GWh/year** (powers ~1.29M Texas households) |
| Named coverage | **100%** (zero manual entry via fallback geocoding pipeline) |
| High/Very High priority assets | **276 buildings (3.2%)** identified for first-wave deployment |
| Highest single asset | **112,712 MWh/year** (industrial, East Abram St, Tarrant County) |
| Dallas County share | **51.7%** of total generation from 58.8% of assets |
| Priority score threshold | ≥0.20 = High/Very High; ≥0.30 = Very High (first-wave) |
| Stakeholder outputs | Interactive Folium HTML map + Power BI dashboard (.pbix) |

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
where A = usable roof area (m²), H = 2,044 kWh/m²/year (5.6 × 365)

---

## Architecture

```
Stage 1 — Building Extraction (Asset_Data_Set.ipynb)
  OSMnx → filter ≥20,000 sq ft commercial, industrial, retail,
           office, warehouse, supermarket across 11 DFW counties
  Project to EPSG:2276 (Texas North Central) for accurate area math
  Output: DFW_Metroplex_Commercial_Assets_20k.csv (8,612 buildings)

Stage 2 — Asset Enrichment (Copy of Restarting_Energy_Project.ipynb)
  Nominatim reverse geocoding → 100% name coverage on 82.8% unnamed records
  Fallback: rule-based label generator [Building Type] on [Street Name]
  Census ACS household counts at tract level (1-mile + 3-mile buffers)
  EV charging station proximity (NREL Alt Fuels Station Locator API)
  Output: DFW_Assets_Enriched.csv, DFW_Assets_Master.csv/.geojson

Stage 3 — Solar Model (CO2_+_cost calculation.ipynb)
  Physics-based generation: E = A × r × H × PR
  CO₂ reduction = Annual kWh × 0.335 kg CO₂/kWh (EPA eGRID ERCOT)
  System capacity (MW) = Usable area (m²) × r / 1000
  Install cost = Capacity (Wdc) × $2.00 (NREL 2025 benchmark)
  Output: final_solar_estimates.csv

Stage 4 — Priority Framework (Forming_categories.ipynb)
  Priority Score = 0.60 × norm(Annual_MWh) + 0.40 × norm(HH_1mi)
  Box-plot tiering (Low/Medium/High) across 3 independent dimensions:
    → Annual solar generation potential
    → Residential household density (1-mile Census buffer)
    → EV charging infrastructure proximity
  Result: 27-category strategic matrix
  Output: Processed_DFW_Assets_Master.csv

Stage 5 — Stakeholder Deliverables
  Folium choropleth map (color-coded by tier, pop-up asset cards)
  Power BI dashboard: Building Asset Mapping.pbix
    Filters: county · building type · generation tier · EV class
```

---

## Key Results

### Generation by Building Type

| Building Type | Count | Avg Area (sq ft) | Avg Annual MWh | Total GWh/yr |
|---|---|---|---|---|
| Industrial | 1,283 | 180,465 | 4,063 | 5,213 |
| Warehouse | 1,929 | ~145,000 | 3,331 | 6,425 |
| Commercial | 2,562 | — | ~2,000 | — |
| Retail | 2,260 | — | — | — |
| Office | 576 | — | highest priority score | — |

Warehouse + Industrial = **63.8% of total generation** from 38.3% of assets.

### Generation by County (Top 4)

| County | Assets | Annual GWh | Share |
|---|---|---|---|
| Dallas | 5,066 | 9,429 | 51.7% |
| Tarrant | 1,498 | — | — |
| Collin | — | — | — |
| Denton | — | — | — |
| Top 4 total | — | — | **94.0%** |

---

## Key Technical Contributions

### 1. Naming Pipeline — 100% Coverage on 82.8% Unnamed Records
OpenStreetMap had 82.8% of records with no building name. Built a two-stage pipeline:
- **Primary:** Nominatim reverse geocoding (amenity → shop → office hierarchy)
- **Fallback:** Rule-based label generator `[Building Type] on [Street Name]`

Result: 100% named coverage with zero manual entry.

### 2. Physics-Based Solar Model — Fully Reproducible from Federal Benchmarks

```python
# Core generation formula
usable_area_m2 = footprint_sqft * 0.0929 * 0.80
annual_kwh = usable_area_m2 * 0.19 * (5.6 * 365) * 0.78

# Business outputs per building
co2_reduction_tons = (annual_kwh * 0.335) / 1000
capacity_mw = usable_area_m2 * 0.19 / 1000
install_cost_usd = capacity_mw * 1_000_000 * 2.00
households_powered = annual_kwh / 14_112  # EIA 2023 TX average
```

All parameters sourced to NREL, EPA, and EIA federal benchmarks.

### 3. Composite Priority Score

```
Priority Score = 0.60 × (Annual_MWh / max_MWh)
              + 0.40 × (HH_1mi / max_HH_1mi)

Thresholds:
  ≥ 0.30 → Very High Priority (first-wave deployment)
  ≥ 0.20 → High Priority
  < 0.20 → Medium / Low Priority
```

276 assets (3.2%) qualify as High or Very High — a deployable, prioritized shortlist.

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
| Categorization | Box-plot statistics (IQR, upper fence) |
| Interactive map | Folium (choropleth + pop-up cards) |
| BI dashboard | Power BI (.pbix) |

---

## Screenshots

**Figure 1 — Building Type Overview**
![Overview](docs/images/Figure1_Overview.png)

**Interactive Folium Map**
Open `file:///C:/Users/Admin/Downloads/DFW_Solar_Repo/outputs/maps/DFW_Energy_Map_Filtered_With_Asset_List.html` in any browser.

**Power BI Dashboard**
Open `outputs/dashboard/Building Asset Mapping.pbix` in Power BI Desktop.
Filters: County · Building Category · Generation Tier · EV Infrastructure Class

---

## Repository Structure

```
DFW-Commercial-Rooftop-Solar-Energy-and-Grid-Readiness-Analysis/
│
├── notebooks/
│   ├── 01_Asset_Data_Set.ipynb                    Stage 1 — OSMnx extraction
│   ├── 02_Restarting_Energy_Project.ipynb         Stage 2 — enrichment & geocoding
│   ├── 03_CO2_cost_calculation.ipynb              Stage 3 — solar model
│   └── 04_Forming_categories.ipynb               Stage 4 — priority framework
│
├── data/
│   ├── raw/
│   │   ├── DFW_Census_Households.csv              Census ACS tract data
│   │   └── EV_Stations_DFW.csv                   NREL EV station locations
│   └── processed/
│       ├── DFW_Metroplex_Commercial_Assets_20k.csv   Stage 1 output
│       ├── DFW_Assets_Enriched.csv                   Stage 2 output
│       ├── DFW_Assets_Master.csv                     Stage 2 master
│       ├── DFW_Assets_Master.geojson                 Spatial master
│       ├── final_solar_estimates.csv                 Stage 3 output
│       └── Processed_DFW_Assets_Master.csv           Stage 4 final
│
├── outputs/
│   ├── maps/
│   │   └── DFW_Energy_Map_Filtered_With_Asset_List.html   ← USE THIS MAP
│   └── dashboard/
│       └── Building Asset Mapping.pbix
│
├── docs/
│   ├── images/
│   │   └── Figure1_Overview.png
│   └── DFW_Solar_Energy_Potential_Manuscript.docx
│
├── category_reference/
│   └── 27 strategic category interpretations.xlsx
│
├── requirements.txt
└── README.md
```

---

## Limitations

- Solar irradiance uses DFW annual average (5.6 hr/day) — does not vary by building orientation or roof pitch
- Usable roof fraction (80%) is standard estimate — actual usable area varies by HVAC, skylights, equipment
- EV proximity uses straight-line distance, not road network routing
- Census household density uses tract-level aggregation — sub-tract variation not captured
- Install cost uses 2025 NREL benchmark ($2.00/Wdc) — actual costs vary by contractor and site conditions
- CAPEX excluded — model covers generation potential and emissions only

---

## Citation

Khade, S. (2026). *Rooftop Solar Energy Potential of Large Commercial and Industrial Buildings in the Dallas-Fort Worth Metroplex.* University of Texas at Arlington, M.S. Data Science.

---

## Author

**Sejal Khade**  
MS Data Science · University of Texas at Arlington · May 2026  
[GitHub](https://github.com/SejalKhade) · [LinkedIn](https://linkedin.com/in/sejallk) · [sejalkhade0023@gmail.com](mailto:sejalkhade0023@gmail.com)
