# Data and analysis README

## Overview

This repository contains results from SWMM simulations and the Jupyter notebook `Analysis.ipynb` used to analyze green stormwater infrastructure (GSI) performance for the study:

From Isolated to Cascading Green Stormwater Infrastructure: Effects of Configuration and Parameter Variability in Combined Sewer Systems.

The analyses compare isolated GSI with cascading green roof–rain garden (GR–RG) and rain barrel–rain garden (RB–RG) configurations. They quantify subcatchment runoff-volume and sewershed combined sewer overflow (CSO)-volume reductions, additional benefits of cascading, variance shares associated with GSI configuration and sampled design parameters, partial rank correlation coefficients (PRCCs), and sensitivity to assumptions about contributing impervious area.

The analysis notebook reads the files listed below; the data files can be distributed separately, provided the relative paths are preserved.

## Directory layout

```text
repository/
├── README.md                  
├── Analysis.ipynb         # Analysis and plotting notebook
└── data/
    ├── run_reduction.npz
    ├── index.npz
    ├── parameter_ensemble.csv
    ├── cso_reduction.pkl
    ├── cso.npz
    ├── baseline_cso.csv
    ├── absulate flow.csv
    ├── run_reduction1.npz
    ├── run_reduction3.npz
    ├── run_reduction5.npz
    ├── cso_reduction1.pkl
    ├── cso_reduction3.pkl
    ├── cso_reduction5.pkl
    ├── data_percent.csv
    ├── summary_by_landuse.csv
    └── sub_percent.csv
```


## Simulation design and ordering

The primary study considers six 6-hour design storms with return periods (1, 2, 5, 10, 25, and 50 years); three implementation densities (25%, 50%, 75% of suitable GSI area); and three GSI configurations (Isolated, Single-cascade, Two-cascade). This yields 54 storm–scenario combinations, each with 1,000 matched Monte Carlo design-parameter realizations.

For arrays and tables with nine scenarios, the notebook expects this exact column/scenario order (storms ordered with 1, 2, 5, 10, 25, 50 years):

| Index (0-based) | Configuration | Density |
|---:|---|---:|
| 0 | Isolated | 25% |
| 1 | Isolated | 50% |
| 2 | Isolated | 75% |
| 3 | Single-cascade | 25% |
| 4 | Single-cascade | 50% |
| 5 | Single-cascade | 75% |
| 6 | Two-cascade | 25% |
| 7 | Two-cascade | 50% |
| 8 | Two-cascade | 75% |


Note: the loading-ratio sensitivity experiment is separate; it compares target contributing-impervious-area-to-RG-area ratios of 1:1, 3:1, and 5:1 at 75% implementation density, using 100 matched parameter realizations. These files have three configurations, in the order Isolated, Single-cascade, Two-cascade.

## File descriptions

### A. Primary runoff and parameter data

| File | Contents / expected structure | Used for |
|---|---|---|
| run_reduction.npz | NumPy archive with array percent_runoff_reduction. Axis order: [storm, scenario, parameter realization, subcatchment]; expected dimensions [6, 9, 1000, N], where N is the number of positions in the processed subcatchment array. Values are simulated runoff-volume reductions relative to the no-GSI baseline, in percent (%). | Runoff reductions, paired additional runoff reductions, variance decomposition, and PRCCs (main Figures 4–5; SI Figures S12–S15). |
| index.npz | NumPy archive containing urban_idx, resi_idx, and green_idx. Each is an integer-position array identifying subcatchments in the urban, residential, and open-space categories, respectively. | Land-use selection for runoff analyses and PRCCs. |
| parameter_ensemble.csv | One record per Monte Carlo parameter realization, with a simulation identifier and sampled GR, RG, and RB design parameters (see column dictionary below). The notebook sorts rows by simulation before calculating PRCCs. | PRCC analysis (SI Figures S13–S15). |

The parameter columns used by the notebook are:

| Column | Meaning | Unit / representation |
|---|---|---|
| simulation | Monte Carlo realization | ID |
| ks_gr_cm_h | GR saturated hydraulic conductivity | cm/h |
| porosity_gr | GR soil porosity | fraction |
| gr_depth_cm | GR soil depth | cm |
| gr_drainmat_cm | GR drainage-mat depth | cm |
| ks_rg_cm_h | RG saturated hydraulic conductivity | cm/h |
| porosity_rg | RG soil porosity | fraction |
| rg_depth_cm | RG soil depth | cm |
| rg_berm_cm | RG berm height | cm |
| field_capacity | Soil field capacity | volumetric fraction |
| initial_moisture | Initial soil moisture content | volumetric fraction |
| suction_head_cm | Soil suction head | cm |
| rb_volume_m3 | Volume of an individual rain barrel | m³ |
| rb_number_per_house | Number of rain barrels per house | count |


### B. Primary CSO and runoff-volume summaries

| File | Contents / expected structure | Used for |
|---|---|---|
| cso_reduction.pkl | Simulated CSO reductions indexed by storm labels '1-Y', '2-Y', '5-Y', '10-Y', '25-Y', '50-Y'. Each storm entry is a pandas DataFrame with Monte Carlo realizations as rows and nine scenarios in the order above as columns. Values are simulated CSO-volume reductions (%) relative to the no-GSI case. | CSO-reduction distributions, paired added CSO reductions, and three-configuration variance decomposition (main Figures 6–7; SI Figure S17). |
| cso.npz | NumPy archive with array containing simulated CSO volumes (m³), indexed by storm, scenario, and Monte Carlo realization. The plotting code expects nine scenarios for each of six storms. | Absolute CSO-volume comparisons (SI Figure S16). |
| baseline_cso.csv | Column cso with the baseline (no-GSI) CSO volume (m³) for each of the six design storms, in the specified storm order. | Baseline lines in SI Figure S16. |
| absulate flow.csv | Land-use-level summary of total lateral inflow/runoff entering the sewer network, in million US gallons, including the no-GSI baseline and scenario medians. Required columns: land_use, configuration, storm_years, implementation_density_pct, baseline_million_gal, inflow_median_million_gal. The notebook converts million US gallons to m³ using 1 million US gal = 3,785.411784 m³. | Land-use runoff volumes (SI Figure S11). |


### C. Loading-ratio sensitivity datasets (100 realizations)

| Files | Contents / expected structure | Used for |
|---|---|---|
| run_reduction1.npz, run_reduction3.npz, run_reduction5.npz | Runoff reduction arrays for loading ratios 1:1, 3:1, and 5:1, respectively. Each has archive key *percent_reduction, with axis order [storm, configuration, realization, subcatchment], expected dimensions [6, 3, 100, N]. Values are runoff-volume reductions (%). | Added runoff reductions and runoff variance-share sensitivity (SI Figures S5–S6). |
| cso_reduction1.pkl, cso_reduction3.pkl, cso_reduction5.pkl | CSO-volume reductions (%) under the same loading ratios, each indexed by the six storm labels and containing a DataFrame with 100 matched realizations × three configurations (Isolated, Single-cascade, Two-cascade). | Added CSO reductions and CSO variance-share sensitivity (SI Figures S5–S6). |


### D. GSI suitability and implemented-area summaries

| File | Contents / required columns | Used for |
|---|---|---|
| data_percent.csv | Aggregated implemented GSI area by Land use, Density, and Configuration, as a percentage of the total sewershed area. Six area columns: GR_iso, GR_cas, RG_iso, RG_cas, RB_iso, RB_cas. _iso denotes independently operating GSI area; _cas denotes cascade-connected area. | Main Figure 3. |
| summary_by_landuse.csv | GSI suitability summary with GI (GR, RG, or RB), Land_use (Urban, Open space, or Residential), and Share_within_landuse_pct (potential area for that GSI type as a percentage of all considered GSI-suitable area in that land-use category). | SI Figure S8 and 10% type-retention threshold. |
| sub_percent.csv | Subcatchment-level implemented GSI area percentages. Required columns include Density_pct, Configuration, Land_use, and GR_iso_pct, GR_cas_pct, RG_iso_pct, RG_cas_pct, RB_iso_pct, RB_cas_pct. Percentages are relative to each subcatchment's area; records with zero GSI implementation are retained for spatial distributions. | SI Figure S9. |

## Analysis workflow and outputs

Analysis.ipynb reads processed datasets and generates visualizations and calculated intermediate tables in memory. Its primary sections are:

1. Runoff analysis: main Figure 4, variance decomposition for main Figure 5 and SI Figure S12, and PRCC summaries for SI Figures S13–S15.
2. CSO analysis:main Figure 6, main Figure 7, and SI Figure S17.
3. Supplemental analyses: absolute CSO volumes (S16), and land-use runoff volumes (S11).
4. Loading-ratio sensitivity: added runoff/CSO benefits (S5) and variance-share sensitivity (S6).
5. GSI implementation and suitability: main Figure 3 and SI Figures S8–S9.


## Running the notebook

Place WRR_analysis.ipynb at the repository root and place all listed input files in the `data/` directory, or update DATA_DIR in the first code cell.
Download runoff reduction from the Zenodo link: 10.5281/zenodo.24429706


## Notes on terminology

- Runoff-volume reduction (%): percentage decrease from the no-GSI reference for the same event and catchment.
- CSO-volume reduction (%): percentage decrease in sewershed CSO volume from the corresponding no-GSI event.
- Added cascading benefit (percentage points, pp): paired difference between a cascading configuration's reduction and the corresponding isolated-GSI reduction at the same storm, density, and parameter realization.
- Variance component shares (%): sums-of-squares shares attributable to configuration, parameter realization, and the non-additive configuration–parameter component. 
- PRCC: partial rank correlation between a sampled GSI design parameter and simulated runoff-volume reduction, controlling for the other parameters included for that scenario.


