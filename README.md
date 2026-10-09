# DFW Commercial Rooftop Solar & EV Grid Readiness Analysis

Geospatial pipeline that identifies **which commercial buildings in Dallas–Fort Worth should get rooftop solar first** — and where EV charging infrastructure is missing. Built across 8,612 large buildings in 11 counties using open data (OpenStreetMap, US Census, NREL, EPA) and Python geospatial tools.

[![Python](https://img.shields.io/badge/Python-3.11-blue.svg)](https://www.python.org/)
[![GeoPandas](https://img.shields.io/badge/GeoPandas-GIS-green.svg)](https://geopandas.org/)
[![Folium](https://img.shields.io/badge/Folium-Interactive%20Maps-teal.svg)](https://python-visualization.github.io/folium/)
[![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow.svg)](https://powerbi.microsoft.com/)

---

## What This Project Does

1. **Pulls** every large commercial/industrial building (≥ 20,000 sq ft) from OpenStreetMap across 11 DFW counties
2. **Models** rooftop solar generation for each building using the PV equation `E = A × r × H × PR` with NREL irradiance data
3. **Enriches** each building with nearby household count (Census ACS5), EV charging station count (NREL AFDC), CO₂ savings, and installation cost
4. **Scores and maps** buildings across 27 strategic combinations of solar potential, household access, and EV infrastructure to surface the highest-impact deployment targets
5. **Runs spatial regression** (OLS → Moran's I → Spatial Error Model) at census-tract level to quantify where solar potential clusters and where EV gaps are largest

---

## Results at a Glance

| Metric | Value |
|---|---|
| Buildings analyzed | **8,612** across 11 counties |
| Total annual solar potential | **15,706 GWh/year** |
| CO₂ emissions avoided | **5.261 M metric tons/year** |
| Households powered (ERCOT grid) | **1,194,170** |
| Total installation cost | **$19.56 Billion** |
| Buildings with **zero** EV stations within 1 mile | **2,609 (30.3%)** — prime co-deployment targets |
| Buildings costing **< $2M** to install | **6,481 (75.3%)** — accessible via PPA / C-PACE financing |
| Top 10% of buildings by size | → **50% of total energy** (Pareto effect) |
| Dallas County share | **51.7%** of total generation |

---

## Maps

### Interactive Strategic Filter Map
`maps/DFW_Energy_Map.html` — open in any browser

Filter 8,612 buildings by **Annual Generation tier**, **Household Population proximity**, and **EV Infrastructure density** simultaneously. Each building shows building type, annual MWh, and location. Sidebar lists all assets matching the active filter.

![Interactive Map — Urban Dallas](Images/map_urban_dallas.png)
*Inner Dallas cluster — colour = building type, marker size = energy potential*

![Interactive Map — Full Metroplex](Images/map_suburban.png)
*Full 11-county coverage — warehouse/industrial corridors visible in south Dallas and west Tarrant*

---

### Census-Tract EV & Solar Priority Map
`maps/DFW_Interactive_Tract_EV_Solar_Map.html` — open in any browser

Bivariate choropleth showing **solar potential per household × EV station presence** for every census tract. Highlights tracts that are high solar / no stations — the best targets for combined solar + EV investment.

![EV Service Gap Map](Images/11_ev_service_gap_map.png)
*Red/orange = high solar potential with no EV stations nearby. These tracts are the highest-priority zones for combined investment.*

---

### Solar Potential Per Household Map
![Per Capita Solar Map](Images/09_percapita_map.png)
*Census-tract choropleth of MWh per household. Suburban and exurban tracts show outsized solar potential relative to residential density.*

---

### Power BI Dashboard
`dashboard/Building_Asset_Mapping.pbix`

![Power BI Full Portfolio](Images/dashboard_full.png)
*Portfolio overview: 8,612 buildings, 15.71M MWh/yr, $19.56B cost, 5.26M tons CO₂/year*

![Power BI Filtered View](Images/dashboard_filtered.png)
*Segment drill-down with Sankey chart showing building type → energy generation flow*

---

## Key Findings

- **Warehouse and industrial buildings** = 37% of buildings but **64% of total energy**. Targeting them first is 1.7× more efficient than uniform deployment.
- **30.3% of buildings** have zero EV stations within 1 mile — a co-deployment opportunity where one investment delivers both solar and EV charging.
- **Solar peaks in July** (1.57B kWh) — perfectly aligned with ERCOT's summer demand peak from AC load, making this a grid-stability investment, not just a green one.
- **75% of buildings cost under $2M** to install — within standard commercial financing, no public subsidy required.

---

## Tech Stack

| | Tools |
|---|---|
| **GIS & Spatial** | OSMnx · GeoPandas · Shapely · EPSG:2276 reprojection · 1-mi & 3-mi buffer analysis · areal interpolation |
| **Maps** | Folium / Leaflet.js · choropleth layers · multi-select buffer zone map · 27-category strategic filter |
| **Data** | pandas · NumPy · US Census ACS5 API · NREL AFDC API · Nominatim geocoding |
| **Spatial Stats** | OLS (statsmodels) · Moran's I (esda) · Spatial Error Model (spreg) · libpysal weights |
| **Dashboard** | Power BI |
| **Viz** | Matplotlib · Seaborn |
| **Environment** | Python 3.11 · Google Colab · Jupyter |

---

## Repo Structure

```
notebooks/
  dfw_energy_analysis_final.ipynb   ← run this (full pipeline end-to-end)

processed/
  final_solar_estimates.csv         ← 8,612 buildings with all metrics
  DFW_Assets_Master.csv / .geojson
  EV_Stations_DFW.csv
  DFW_Census_Households.csv
  street_checkpoint.csv             ← Nominatim geocoding cache (saves ~2.5 hrs)

maps/
  DFW_Energy_Map.html               ← interactive strategic filter map
  DFW_Interactive_Tract_EV_Solar_Map.html  ← census-tract EV + solar map

dashboard/
  Building_Asset_Mapping.pbix       ← Power BI

category_reference/
  27_strategic_category_interpretations.xlsx
```

---

## How to Run

Open `notebooks/dfw_energy_analysis_final.ipynb` in **Google Colab** or Jupyter. Requires two free API keys:

- Census: https://api.census.gov/data/key_signup.html
- NREL: https://developer.nrel.gov/signup/

Set as Colab Secrets (`CENSUS_API_KEY`, `NREL_API_KEY`). The geocoding cache (`street_checkpoint.csv`) is pre-loaded so a fresh run takes minutes, not hours.

---

## Data Sources

OpenStreetMap (OSMnx) · US Census ACS5 2024 · US Census TIGER 2025 · NREL AFDC API · NREL NSRDB · EPA eGRID ERCOT (0.386 kg CO₂/kWh) · NREL cost benchmark ($2.00/W)
