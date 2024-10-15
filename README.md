# afg_viirs_analysis / Afghanistan VIIRS Data Analysis
Extract VIIRS and VIIRS-like data, pre-process them, and analyze their time-series NTL intensity while excluding the NTL pixels that are assumed to be emitted from the coalition military bases.

All Jupyter notebooks are optimized for the [Google Colab](https://colab.google/) environment. The pre-processing of monthly VIIRS data in an X-array dataset is memory-heavy, so you likely need to upgrade your Google Colab account from a free to a paid one.

As of 2024 May 9, the latest VIIRS (2014 JAN–2024 MAR, monthly) and VIIRS-like (2000–2022, yearly) datasets within the Afghanistan national boundary have already been stored. So, at this moment, you don't need to update the raw datasets. Check the VIIRS downloader if you need the latest datasets.

## Quick start:
(1) Copy and paste all materials to your own Google Colab environment. If you are good at Python coding, a new Conda environment could also work but you likely need to modify some code blocks (especially around libraries and paths).

(2) Unless you need to update the raw VIIRS (VIIRS-like) data, you can skip `VIIRS_download.ipynb` and just start with `VIIRS_analysis.ipynb`. For detailed instructions, check the comments inside the notebooks.

(3) `VIIRS_analysis.ipynb` supports both VIIRS and VIIRS-like processing. Per runtime, you should select which one should be processed. Both of them cannot work simultaneously at a single runtime. Read notes in the notebook.


## Filtering pipelines:
There are 2 filtering pipelines are available:

**(A) OpenStreetMap and Standard Deviation threshold (OSM+SDV) filtering:**

To mask NTL from potential military bases, this pipeline uses military-labeled polygons in OSM as well as the Standard Deviation (SDV, Std.Dev) thresholding filter. Here, the 'SDV' part needs to be understood carefully in that it simply applies a `Std.Dev >= 8.477` threshold across all monthly VIIRS data (2014JAN–2023DEC, as of 2024 May 9) REGARDLESS OF the potential opening/closing of bases WITHOUT eye-visual assessment of military polygons. The filtered VIIRS data shall be aggregated at the district level.


**(B) Expert-verified SDV-based & OpenStreetMap Miltary tagged polygon filtering:**

To mask NTL from potential military bases, this pipeline applies _time-selectively_ expert-eye-check, SDV-based mask polygons and OSM_miltary base polygons.

A quick process note is that:

- (1) Polygonize VIIRS pixels whose `Std.Dev >= 8.477` for large bases and `Std.Dev >= 2.518` for small bases.
- (2) The WB expert team verified each SDV-based polygon one by one with eyes (resulting in 52 polygons = verified military bases).
- (3) Extract the VIIRS NTL values only within the 'expert-verified' potentially military polygons. Be sure that the team tried to be as comprehensive as possible, but there must be both inclusion and exclusion errors.
- (4) Both annual mean and annual max for each 'expert-verified' polygon and constructed a composite index using both mean and max (Mean-Max index) to identify the opening and closing of each base (polygon). Here, `the Mean-Max Index >= 0.5` is considered to be 'active coalition bases' and `0.5 > the Mean-Max Index >= 0.25` is considered to be 'active Afghan bases.'
- (5) Based on this 'opening-closing' timing matrix (see below), the expert-verified polygons are _time-selectively_ applied to the monthly VIIRS data to mask out the NTL values within the expert-verified polygons only when the polygon is 'active.'
- (6) A function applied later tweaks the "opening-closing" timing matrix so that each polygon is always classified as a military base. Instead of marking the base as inactive, it assigns the polygon as either a coalition or Afghan base, based on ealrier years values, ensuring continuous military status with shifting designations over time.

- This version incorporates and overlaps the OSM military-tagged polygons into the process flow.

## Technical notes:
- VIIRS datasets: `VIIRS_analysis.ipynb` has an anomaly-NTL reduction process (Filter-1: Noise reduction (background and max anomalies)) based on the NTL background noise mask (average_masked in [VIIRS Nighttime Day/Night Annual Band Composites V2.1](https://developers.google.com/earth-engine/datasets/catalog/NOAA_VIIRS_DNB_ANNUAL_V21#bands)) and the avg_rad < 300 max-cap threshold. This process should be run before the other filtering processes.
- VIIRS-like datasets: Read its [foundational technological paper](https://essd.copernicus.org/articles/13/889/2021/) by Zuoqi Chen et al. (2021).
- Also read [Global NPP-VIIRS-like nighttime light (2000-2022)](https://gee-community-catalog.org/projects/npp_viirs_ntl/)


## Contributors
- `Eigo Tateishi` (main coder) / No longer working on this repo/project
- `Ivo Teruggi` (current lead developer) / Currently maintaining the repo/project
- :star:`Walker Kosmidou-Bradley` (expert input) / Contact person
- :star:`Oscar Eduardo Barriga Cabanillas` (expert input) / Contact person

The World Bank (2024)
