# ⚡ DFW Commercial Rooftop Solar Energy & Grid Readiness Analysis 
A geospatial analytics project focused on finding the strongest clean-energy opportunity sites across the Dallas–Fort Worth area.

I built this project to answer a practical planning question:

**Which assets look most promising when we combine solar generation potential, nearby household reach, and local EV charging opportunity?**

Using building-level asset data, **Census ACS5 households**, and the **NREL alternative fuel stations API**, I created a scoring pipeline that ranks energy assets across DFW and exports the results as dashboard-ready files, maps, and geospatial layers.

---


## 🖼️ Dashboard / map preview

<img width="1727" height="399" alt="image" src="https://github.com/user-attachments/assets/bbf3c765-f4a9-404c-975e-a9a38294154f" />
<img width="1772" height="615" alt="image" src="https://github.com/user-attachments/assets/a143c102-d381-4894-b75a-2fcfa8b465d1" />
<img width="1051" height="628" alt="image" src="https://github.com/user-attachments/assets/17f6311b-a3ff-4dba-969e-140e31fe2513" />
<img width="1761" height="753" alt="image" src="https://github.com/user-attachments/assets/312c8831-4b01-4257-85d4-287f8c1fa2b3" />



## ✨ What this project does

- analyzes **357 energy-related assets** across the DFW region
- estimates rooftop and canopy solar generation potential
- measures nearby household reach using **1-mile and 3-mile buffers**
- counts nearby public EV charging stations
- builds a weighted priority score to rank candidate sites
- exports results to **CSV**, **GeoJSON**, **Folium map**, and **dashboard-friendly outputs**

---

## 🗺️ Why I built it

Energy planning gets more useful when it moves beyond a single metric.

A site may have strong solar potential but limited nearby demand.  
Another may sit near many households but already have enough EV infrastructure nearby.

I wanted to build something that balances all three:

- **generation potential**
- **household reach**
- **EV opportunity gap**

That made the project a nice mix of geospatial analysis, public API work, scoring logic, and visual storytelling.

---

## 📊 Project highlights

- ranked **357 DFW assets**
- pulled household data across **8 counties** using **Census ACS5**
- used **GeoPandas buffer joins** to estimate nearby households
- downloaded **119 public EV stations** in the study area using the **NREL API**
- built a weighted priority score:
  - **50%** generation potential
  - **30%** households nearby
  - **20%** EV gap
- produced:
  - ranked CSV outputs
  - GeoJSON layers
  - an interactive Folium map
  - Plotly-ready visuals

---

## 🧪 Data sources

- **Building / asset dataset** for Arlington / DFW sites
- **Census ACS5 API** for household counts
- **TIGER/Line block group geometry**
- **NREL Alternative Fuel Stations API** for public EV charging locations

---

## 🛠️ Tools used

`Python` `Pandas` `GeoPandas` `Folium` `Plotly` `NumPy` `Requests` `Jupyter Notebook`

---

## 🧱 How the pipeline works

### 1) Load and prepare asset data
The starting point is a building-level dataset with location and size information.  
I cleaned the coordinates, standardized useful text fields, inferred asset names where needed, and created categories for different asset types.

### 2) Estimate solar generation potential
For each asset, I estimated:
- usable rooftop area
- usable canopy area
- annual solar generation potential
- household support estimates based on annual kWh generation

### 3) Add nearby household demand
Using **Census ACS5** block group data, I created **1-mile and 3-mile buffers** around each asset and allocated households based on spatial overlap.

This made it possible to estimate:
- households within 1 mile
- households within 3 miles
- relative demand reach around each site

### 4) Add EV charging context
I pulled public EV charging stations from the **NREL API** and counted how many stations fall within **3 miles** of each asset.

This helps identify places where infrastructure opportunity may still be open.

### 5) Score and rank assets
I built a weighted scoring model to prioritize sites using:

- **50%** generation potential  
- **30%** households nearby  
- **20%** EV gap  

Higher generation improves the score.  
More nearby households improve the score.  
Fewer nearby EV stations improve the opportunity score.

### 6) Export outputs
The final pipeline creates multiple outputs for analysis and presentation:
- ranked CSV files
- GeoJSON layers
- interactive Folium map
- dashboard-ready summary data
- project insight text output

---

## 🧮 Priority score

The ranking model combines three signals into one final score:

**Priority Score = 50% Generation Potential + 30% Households Nearby + 20% EV Gap**

This was designed to avoid over-prioritizing only large buildings or only dense neighborhoods.  
Instead, it gives a more balanced view of infrastructure opportunity.

---

## 📌 Key metrics

### Solar / asset metrics
- building square footage
- usable roof area
- usable canopy area
- rooftop generation (kWh/year)
- canopy generation (kWh/year)
- total generation (kWh/year)

### Reach / demand metrics
- households within 1 mile
- households within 3 miles
- estimated households supported

### EV opportunity metrics
- EV stations within 3 miles
- EV gap score

### Final ranking metrics
- generation score
- household score
- EV gap score
- priority score
- rank

---

## 🔍 A few takeaways from the notebook

- The pipeline successfully ranked all **357 assets** in the study area.
- The strongest asset in the notebook output reached about **370M kWh/year** in estimated annual generation.
- The top-ranked site showed a priority score of **0.792**.
- Household density within **3 miles** had one of the strongest positive relationships with the final score.
- The asset classification step still leaves a meaningful share of records as `unknown`, which is one area I’d improve in a future version.

