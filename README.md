# Road Pavement Cracking & Tidal Flooding in The Hague, Norfolk, VA

**OEAS 805: Advanced Environmental Data Science (Fall 2026) · Nick Carpenter**

## Project summary

Chronic high-tide ("nuisance") flooding is becoming more frequent in Norfolk, VA as sea level rises. This project
asks whether **road pavement cracking** in the low-lying Hague neighborhood is associated with **how often the
pavement floods**.

The study combines several sources, all of which are aligned to the 0.02 m imagery grid:

- a **2 cm drone orthomosaic and 0.5 m drone DEM** flown at low tide on 3 October 2025;
- a **crack map** classified from that imagery;
- a NOAA **VDatum** surface that expresses elevation as height above Mean Higher High Water (MHHW);
- the **Ezer (2022)** tide-gauge flood-frequency relation, which converts elevation into modeled flood days per year;
- **VGIN road centerlines with VDOT traffic volumes (AADT)** as a control.

This repository is a simplified, **fully open-source** version of a larger ArcGIS Pro workflow. Every external
dataset is read straight from its public ArcGIS REST API for the study-area extent only, so the repository stays
small.

## Project status and planned analysis

This repository currently holds the first notebook, **`NB00_EDA.ipynb`**, an exploratory data analysis (EDA) of every
dataset: description and access, spatial and temporal coverage, integration and overlap, descriptive statistics,
outliers, and a first look at how crack density relates to flood frequency, elevation and traffic.

**Planned notebooks** will use that relationship to explore how road cracking could change as sea level rises,
following the approach of the original `NB05` projection notebook:

1. **Study cells.** The analysis is limited to road-only 5 m cells: sidewalk/driveway cells and road cells without a
   traffic (AADT) count are excluded.
2. **Scenario.** Cells are projected for 2030, 2040, 2050, 2060, 2070, 2080, 2090 and 2100 under the NOAA 2017
   Intermediate-High relative sea-level-rise scenario (USACE, 2022). As a simple thought experiment, no road
   maintenance is assumed and traffic and all other cell attributes are held constant.
3. **Flood exposure.** As was done for 2025, a flood-days-per-year raster is computed for each decade from the DEM, the
   VDatum surface and the projected sea level (Ezer, 2022).
4. **Cracking.** The modeled contribution of flood frequency to crack density is applied to each cell's projected flood
   days, giving the expected increase in crack density per cell and decade.
5. **Outputs.** The projected flood-days rasters for all decades are stored in one output folder, and the projected
   crack-density values are stored on the 5 m cell polygons in another, so they can be mapped as choropleths for each
   decade.

These later notebooks are not part of this repository yet.
## Repository structure

```
├── README.md
├── oes805_proj.yml               conda environment (conda env export)
├── oes805_proj.txt               exact package list (conda list --explicit)
├── CODE/
│   ├── NB00_EDA_v4.ipynb         exploratory data analysis of every dataset
│   └── scratch_temp/             throwaway API download cache (safe to delete)
├── DATA/
│   ├── 00_Raw_data/              original downloads, by source; never modified (optional: everything can come from the API links)
│   ├── 01_Raw_Data_Custom/       hand-made inputs (crack raster, road mask, accuracy points,
│   │                             sidewalk polygons, extra centerlines, water surface); never modified
│   ├── 02_Clean_Data/            AOI-filtered, reprojected, snapped and joined data (OUT_DATA_NB##_Sec## folders)
│   └── 03_Results_Output_data/   result datasets of the major analysis sections
├── FIGURES/
│   ├── Figures/                  charts (OUT_FIGURES_NB##_Sec##/Fig##_Name_YYYYMMDD_HHMMSS.png)
│   └── Maps/                     maps (same naming)
└── REPORTS/
    ├── 01Papers_Sources_Downloaded/
    └── 02Notes/                  notes written by the notebooks (OUT_REPORTS_NB##_Sec##)
```

## Data

| Dataset | Type | Access |
|---|---|---|
| Hague 2025 drone imagery (0.02 m RGB) | primary data | [ArcGIS item](https://www.arcgis.com/home/item.html?id=ac0a20f8293143c191a5039b6f161cbe), tiled image service |
| Hague 2025 drone DEM (0.5 m, NAVD88) | primary data | [ArcGIS item](https://www.arcgis.com/home/item.html?id=c7e6e2c791fc4cc895db55a59be804a6), tiled image service |
| NOAA VDatum conversion (MHHW in NAVD88) | external | [Esri Living Atlas](https://www.arcgis.com/home/item.html?id=a7238c20bfc445be97b3d32a49e5b363) |
| VGIN Road Centerlines + VDOT AADT (layer 5) | external | [VGIN](https://vgin.vdem.virginia.gov/datasets/cd9bed71346d4476a0a08d3685cb36ae/about) feature service |
| City of Norfolk driveways / sidewalks | external | [driveways](https://norfolkgisdata-orf.opendata.arcgis.com/datasets/ORF::driveway-city-of-norfolk/about), [sidewalks](https://norfolkgisdata-orf.opendata.arcgis.com/datasets/ORF::sidewalk-city-of-norfolk/about) |
| NOAA relative sea level trend, Sewells Point (8638610) | external | [NOAA CO-OPS](https://tidesandcurrents.noaa.gov/sltrends/sltrends_station.shtml?id=8638610) CSV + API |
| NOAA (2017) Intermediate-High SLR, Sewells Point | external | coded in the notebook (USACE, 2022; Sweet et al., 2017) |
| Crack raster, road mask, accuracy points, sidewalk polygons, extra centerlines, water surface | custom | `DATA/01_Raw_Data_Custom` |

Full descriptions, how each custom file was made, and the references are in the first cells of `CODE/NB00_EDA_v4.ipynb`.

## How to run

1. Download the environment file `oes805_proj.yml` to your project folder, then activate your environment `conda activate oes805_proj`.
   The notebook's first cell pip-installs `lerc` (Esri's LERC decoder for the image-service tiles) if it is missing.
2. Open `CODE/NB00_EDA_v4.ipynb`, set `PROJECT_ROOT` in **00.0 (Step B)** (or `None` to auto-detect), and run all cells.
3. The first run downloads the study-area data (cached in `CODE/scratch_temp/NB00/api_cache`) and aggregates the
   2 cm crack raster. Later runs reuse both.

## References

- Ezer, T. (2022). A demonstration of a simple methodology of flood prediction for a coastal city under threat of sea level rise: The case of Norfolk, VA, USA. *Earth's Future, 10*(9), e2022EF002786. https://doi.org/10.1029/2022EF002786
- Sweet, W. V., Kopp, R. E., Weaver, C. P., Obeysekera, J., Horton, R. M., Thieler, E. R., & Zervas, C. (2017). *Global and regional sea level rise scenarios for the United States* (NOAA Technical Report NOS CO-OPS 083). NOAA.
