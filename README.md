[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.6426191.svg)](https://doi.org/10.5281/zenodo.6426191)

# Sea Level Impacts Input Dataset by Elevation, Region, and Scenario (SLIIDERS)

This repository hosts the code used to create the [SLIIDERS-ECON and SLIIDERS-SLR](https://doi.org/10.5281/zenodo.6426191) datasets. The SLIIDERS datasets contain current and forecasted physical and socioeconomic metrics from 2000-2100 - organized by coastal segment, elevation slice, and scenario - for use as inputs to global coastal climate impacts research.

**SLIIDERS-ECON** contains socioeconomic variables, varying horizontally and vertically over space. **SLIIDERS-SLR** contains Monte Carlo projections of Local Sea Level Rise under different emissions and ice sheet dynamics assumptions, based on the outputs of [LocalizeSL](https://github.com/bobkopp/LocalizeSL). Coastal segments in SLIIDERS-ECON can be matched to gridded LSLR projections in SLIIDERS-SLR via the `SLR_site` key.

All work utilizing this code or the resulting SLIIDERS datasets should cite Depsky, Bolliger et al. 2022 (in prep.). See [License](#license) for details.

## Installation

Most users will want to just use the datasets directly, accessible at the DOIs linked above. If you wish to recreate and/or modify the datasets, which we encourage, you will need to run the Jupyter notebooks in this repository. A collection of helper functions, organized into a Python package, is necessary to run the notebooks and can be found within the `sliiders` directory. A simple pip install will install this package

```bash
pip install -e sliiders
```

In addition, you will need to have [Dask Gateway](https://gateway.dask.org) installed and configured to execute the parallel, Dask-backed workflows contained in this repo. Advanced users can use other Dask Cluster backends (including simply running [Dask Distributed](https://distributed.dask.org) locally), but doing so will require modifying the cluster setup portion of notebooks that employ dask.

A Conda environment file better specifying a full environment needed to execute all of the workflows in this repo is in development and will be posted when complete.

## Filepaths and other settings

All filepaths and settings for the notebooks can be found within `settings.py`. Before moving onto executing different parts of this repository, please adjust these settings to match your directory structure and desired values. Most values will not need to be updated unless you change a particular dataset. However, at minimum you should:

1. Update the `DIR_DATA` filepath within this file to point to the root directory within which all of the data consumed and generated by this workflow will live.
2. Update `DASK_IMAGE` to point to a Docker Image that you will use for Dask workers (advanced users not using Dask Gateway may not need this parameter).

## Package Structure

* `sliiders`: Contains `.py` files with essential settings and functions for the SLIIDERS workflow
  * `settings.py`: Contains essential settings, including various parameters and data storage directories
  * `gcs.py`: Contains functions related to the use of Google Cloud Storage (GCS). Users running workflows locally or on a different cloud provider are encouraged to contribute similar modules for other contexts.
  * `io.py`: Contains various I/O-related functions
  * `spatial.py`: Contains functions for executing spatial and geographic operations including those related to shapefiles, grid-cell level operations, and more.
  * `dask.py`: Contains utility functions for working with dask clusters
  * `country_level_ypk.py`: Contains functions for cleaning and working with country-level socioeconomic data, especially for the workflow in `notebooks/country_level_ypk`

* `notebooks`: contains the workflows to create SLIIDERS-ECON and SLIIDERS-SLR.

## Instructions (Generating SLIIDERS-ECON and SLIIDERS-SLR)

To generate **SLIIDERS-ECON** and **SLIIDERS-SLR**, please follow the directions in `notebooks/README.md` and other readme files in the subdirectories within `notebooks` to learn about how to execute the workflows.  
\
The list and order of notebooks to run is reproduced in full here, along with any necessary manual steps. Click the `docs` link for each workflow to navigate to the relevant directory's page.

1. `create-SLIIDERS-SLR` ([docs](notebooks/create-SLIIDERS-SLR)): Workflow to generate **SLIIDERS-SLR**
   1. `download-ifile-to-gcs.ipynb`
   2. `convert-mat-version.ipynb`
   3. `generate-projected-lsl.ipynb`
   4. `retrieve-num-gcms.ipynb`
   5. `process-localizesl-output.ipynb`
2. `create-SLIIDERS-ECON` ([docs](notebooks/create-SLIIDERS-ECON)): Workflow to generate **SLIIDERS-ECON**
   1. `download-sliiders-econ-input-data.ipynb`
   2. `country_level_ypk` ([docs](notebooks/create-SLIIDERS-ECON/country_level_ypk)): Workflow for organizing and projecting GDP (Y), population (P), capital stock (K), and related variables for historical (1950-2020) and future (2010-2100) timelines.
      1. `ypk1_prep_clean.ipynb`
      2. `ypk2_reorg_and_impute_ypk.ipynb`
      3. `ypk3_demo_ratios_historical_reg.ipynb`
      4. `ypk4_impute_hist_capital.ipynb`
      5. `ypk5_projected_yp.ipynb`
      6. `ypk6_projected_capital.ipynb`
   3. `exposure` ([docs](notebooks/create-SLIIDERS-ECON/exposure)): Workflow to generate present-day exposure grid.
      1. `1-create-coastline-segments.ipynb`
      2. `2-create-segment-regions.ipynb`
      3. `3-fill_missing_litpop_with_geg.ipynb`
      4. `4-vectorize-wetlands.ipynb`
      5. `5-get_positive_elev_tiles.ipynb`
      6. `6-generate_datum_conversion_grid.ipynb`
      7. `7-create_dem_mss.ipynb`
      8. `8-generate_protected_areas.ipynb`
      9. `9-generate_exposure_tiles.ipynb`
      10. `10-combine_exposure_tiles.ipynb`
   4. `create-SLIIDERS-ECON.ipynb`

The resulting datasets can be found at these paths, defined in `settings.py`:  
**SLIIDERS-ECON**: `PATH_SLIIDERS_ECON`  
**SLIIDERS-SLR**: `PATH_SLIIDERS_SLR`

## Support

Please file an issue for any problems you encounter.

## Contributing

We encourage community contributions. At the moment, we have no contribution template. Please fork the project and file a Merge Request to propose your addition. Clearly define the contribution that the Merge Request is making and, when any issues have been resolved, we will merge the new code.

## Authors

The original authors of this code include:
* Daniel Allen
* Ian Bolliger
* Junho Choi
* Nicholas Depsky

## License

This code is licensed under the [MIT License](./LICENSE). However, we request that wherever this code or the SLIIDERS datasets are used, that the underlying manuscript (Depsky et al. 2022) is cited. A citation guide will be posted once the manuscript preprint is available.
