# From Rising Tides to Broken Streets: Linking Flood Frequency & Pavement Cracking Under Projected Sea-Level Rise in The Hague, Norfolk, VA**

**Nick Carpenter** 

**OEAS 805: Advanced Environmental Data Science (Fall 2026) · Old Dominion University**

This project tests whether streets that flood more often at high tide are more cracked, and it projects how much more cracking rising seas could cause by 2080 in Norfolk's low-lying Hague neighborhood.

## How to Run the Code

1. **Download the repository.** On GitHub, click **Code → Download ZIP** and unzip it, or run `git clone`. Keep the folders as they are: the project folder (`OEAS805_ncarp_finalproj`) must still contain `CODE`, `DATA`, and `FIGURES`. 
2. **Open a terminal in the project folder.** Open your preferred terminal (e.g., Anaconda Prompt on Windows, or Terminal on macOS/Linux) and move into the unzipped `OEAS805_ncarp_finalproj` folder. Replace the placeholder with the folder's location on your computer:
   ```
   cd /d "C:\Users\..........\OEAS805_ncarp_finalproj"
   ```
   - On macOS/Linux, or in Windows PowerShell, leave out `/d`: `cd "/Users/........../OEAS805_ncarp_finalproj"`
   - Run the next step from this folder, because `oes805_proj.yml` is saved in it.
3. **Create the Python environment** from `oes805_proj.yml` (in the main folder of the repository), then start Jupyter from it:
   ```
   conda env create -f oes805_proj.yml
   conda activate oes805_proj
   jupyter lab
   ```
4. **Open `CODE/NB01_FinalProj_ReportDraft.ipynb`.** It produces all the analysis and figures of the final report.
5. **Set the project folder.** In cell **00.2 (Step A)**, replace the placeholder with the location of the project folder on your computer:
   ```python
   PROJECT_ROOT = r"C:\Users\..........\OEAS805_ncarp_finalproj"
   ```
   You can also write `PROJECT_ROOT = None`, which finds the folder automatically, as long as the notebook stays in `CODE`.
6. **Choose what to save** (also in 00.2 Step A):
   - The settings `SAVE_DATA_OUTPUTS`, `SAVE_REPORT_FIGURES`, and `SAVE_SUPPLEMENTARY_FIGURES` are set to `False` by default to rerun the analysis without overwriting the data and figures that come with the repository.
   - If desired, set them to `True` to write new copies to `DATA` and `FIGURES`.
7. **Run all cells** (**Run → Run All Cells**).
   - The downloads from the data providers' web services (in `CODE/scratch_temp`) and the slow pixel counts (in `DATA/02_Clean_Data`) are included in the repository. Hence, the notebook reuses them instead of redoing them.
   - The first code cell installs the `lerc` package (Esri's decoder for the image tiles) if it is missing. That step needs an internet connection.

**You do not need to run `CODE/NB00_EDA.ipynb` to review the final report.** It is an exploratory data analysis: run it only if you want a closer look at the datasets used in the project. NB01 does not depend on it. To run it, set `PROJECT_ROOT` in its cell **00.0 (Step B)** the same way.

## Project Description

**What.** Chronic tidal flooding is becoming more frequent in Norfolk, VA, as relative sea level rises. Water that soaks into a road's foundation weakens it, and traffic then cracks the surface. This project asks two questions about the streets around the Hague inlet:

1. Is present-day (2025) crack density higher where the pavement floods more often, after accounting for traffic and surface type?
2. If nothing changed except how often the streets flood, how much could cracking increase by 2040, 2060, and 2080 under the NOAA (2017) Intermediate-High sea-level-rise scenario?

**How.**

- A 2 cm drone image and a crack map classified from it give the crack density of each 5 m × 5 m pavement cell.
- A drone elevation model, the NOAA VDatum tidal surface, and an empirical tide-gauge equation (Ezer, 2022) give each cell's flood frequency (days flooded per year).
- A fractional logistic regression relates crack density to flood frequency, with traffic/surface class as a control. Its p-values and its evaluation account for the strong similarity of neighboring cells (spatially robust standard errors and spatial cross-validation).
- The fitted relationship is applied to each cell's projected flood frequency.

**Why these tools.**

- The project is written in open-source Python (geopandas, rasterio, statsmodels, scikit-learn, and PySAL), so anyone can rerun it without an ArcGIS license.
- Every external dataset is requested straight from its provider's web service, for the study area only, which keeps the repository small.

**Challenges**

- **Separating flooding from traffic.** The busiest streets are also among the most flooded, so the model has to separate the two effects.
- **Unmeasured local factors.** Cracking also depends on things that vary from street to street and were not measured, such as pavement age, repaving history, and drainage. The flood effect is therefore an association, not proof of cause.


## The Report and Its Figures

- **Report.** The final report draft is `REPORT/Report01_OEAS805_FinalProj_ncarp_Peer_Review_Draft.pdf`.
- **Full-resolution figures.** The figures in the PDF are reduced in size. To see them at full resolution, open the subfolders of `FIGURES`, which are sorted by report section:

  | Folder | Contents |
  |---|---|
  | `FIGURES/01_Intro_Figures` | Report Figures 1–2 (sea level at Sewells Point; study area) |
  | `FIGURES/02_Methods_Figures` | Report Figures 3–6 (crack detection; analysis cells; fitted model; projection summary) |
  | `FIGURES/03_Results_Figures` | Report Figures 7–8 (flood-frequency maps; crack-density maps) |
  | `FIGURES/04_Supplementary_Figures` | Supplementary figures FigS01–FigS10 (visual checks and model evaluation found throughout NB01 code; not in the report) |
  | `FIGURES/05_Supplementary_Tbl_Figs` | Images of key result tablesfound throughout NB01 code, TblS01–TblS03 (not in the report) |

- **Why some maps look different.** The maps in the report differ from the maps the notebook draws. I made the report maps by hand in ArcGIS Pro from the same data the notebook uses and produces, because ArcGIS Pro gives better control over labels, layout, and symbols. The notebook's maps are simpler, code-only versions for checking the data. The non-map charts (e.g., the sea-level plot and the model plots) are the same in both.

## Repository Structure

```
OEAS805_ncarp_finalproj/
├── README.md
├── oes805_proj.yml          Python environment (conda)
├── oes805_proj.txt          exact package list of the environment (conda list --explicit)
├── NB00_EDA_Claude_Chat_Transcript.txt
│                            transcript of the Claude chat for NB00_EDA
├── NB01_ReportDraft_Claude_Chat_Transcript.txt
│                            transcript of the Claude chat for NB01 and the project report
├── NB01_ReportDraft_Notebooklm_Chat_Transcript.txt
│                            transcript of the NotebookLM/ Gemini Notebook chat for writing the report draft
├── CODE/
│   ├── NB01_FinalProj_ReportDraft.ipynb   analysis and figures of the final report
│   ├── NB00_EDA.ipynb                     optional exploratory data analysis
│   └── scratch_temp/                      cached downloads and temporary files of both notebooks
├── DATA/
│   ├── 01_Raw_Data_Custom/  hand-made inputs: crack map, pavement mask, accuracy points, sidewalks,
│   │                        extra road centerlines, water surface, and crack-model training lines
│   ├── 02_Clean_Data/       the datasets after clipping, reprojecting, and aligning them to one grid
│   └── 03_Results_Data/     flood-frequency rasters, analysis cells, model tables, and projected crack density
├── FIGURES/                 full-resolution figures, one subfolder per report section (see above)
└── REPORT/
    └── Report01_OEAS805_FinalProj_ncarp_Peer_Review_Draft.pdf
```

## Data

| Dataset | Source |
|---|---|
| 2025 drone imagery (2 cm) and elevation model (0.5 m) | Blake Steiner, Old Dominion University ([imagery](https://www.arcgis.com/home/item.html?id=ac0a20f8293143c191a5039b6f161cbe), [elevation](https://www.arcgis.com/home/item.html?id=c7e6e2c791fc4cc895db55a59be804a6)) |
| Tidal datum surface (mean higher high water) | [NOAA VDatum Conversion](https://www.arcgis.com/home/item.html?id=a7238c20bfc445be97b3d32a49e5b363) |
| Road centerlines with traffic volumes (AADT) | [Virginia Geographic Information Network (VGIN)](https://vgin.vdem.virginia.gov/datasets/cd9bed71346d4476a0a08d3685cb36ae/about) |
| Driveways | [City of Norfolk](https://norfolkgisdata-orf.opendata.arcgis.com/datasets/ORF::driveway-city-of-norfolk/about) |
| Sea-level record, projections, and tidal datums at Sewells Point | [NOAA Tides & Currents APIs](https://api.tidesandcurrents.noaa.gov/) |
| Intermediate-High sea-level values used in the flood model | [USACE Sea-Level Change Curve Calculator](https://cwbi-app.sec.usace.army.mil/rccslc/slcc_calc.html) |
| Crack map, pavement mask, accuracy points, sidewalks, extra centerlines, and water surface | Made by the author (`DATA/01_Raw_Data_Custom`) |

How each dataset was accessed and prepared is described in NB01, Section 01.

## Credits

- **Author:** Nick Carpenter, Old Dominion University.
- **Drone imagery and elevation data:** Old Dominion University (Steiner, 2026).
- **Flood-frequency method:** Ezer, T. (2022). *Earth's Future*, 10(9). https://doi.org/10.1029/2022EF002786
- **Sea-level scenario:** Sweet, W. V., et al. (2017). NOAA Technical Report NOS CO-OPS 83. https://doi.org/10.7289/v5/tr-nos-coops-083
- **Full references:** in  `REPORT/Report01_OEAS805_FinalProj_ncarp_Peer_Review_Draft.pdf` and in the References cell of NB01.
