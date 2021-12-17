# CVD1-HF analysis

[![Snakemake](https://img.shields.io/badge/snakemake-≥5.7.0-brightgreen.svg)](https://snakemake.bitbucket.io)

A snakemake workflow to reproduce analysis for the Olink CVD-1 - HF project

## Authors

* Albert Henry (@alhenry)

## Usage

The following describes a step-by-step procedure to reproduce the analysis.
Code blocks represent input commands for command line interface (e.g. Terminal app on Mac OS)

### Step 1: Install Conda & Snakemake

Install [Conda](https://conda.io/projects/conda/en/latest/user-guide/install/index.html) / [Miniconda](https://docs.conda.io/en/latest/miniconda.html) (recommended for a fresh installation)

Install Snakemake using:

    conda create -c bioconda -c conda-forge -n snakemake snakemake

For installation details, see the [instructions in the Snakemake documentation](https://snakemake.readthedocs.io/en/stable/getting_started/installation.html).


### Step 2: Obtain a copy of this workflow

Using `git`, [clone](https://help.github.com/en/articles/cloning-a-repository) this repository to your local system:

    git clone https://github.com/alhenry/cvd1-hf.git

Enter the newly creted (cloned) directory:

    cd cvd1-hf


### Step 3: Execute workflow

Activate the conda environment:

    conda activate snakemake


Execute the workflow locally via

    snakemake --use-conda -c all


### Step 4: Inspect results

A successful execution should create a `results` folder containing 2 files:
`res_Observational.csv` (observational results) and `res_MR_aggregate.csv` (MR results)


## Notes

### Can I run the workflow without snakemake?

Yes, but it requires some tweaks and some familiarity with R.
The main analysis scripts are located in the `workflow/scripts/` folder.
These are standard R scripts embedded in a snakemake pipeline, which can be modified by substituting the relevant snakemake objects with local objects to run specific parts of the analysis.

### Change in mr_egger() function in MendelianRandomization package

The sensitivity analysis performed in the present analysis involves running
a MR-Egger regression accounting for correlation between instruments
with `mr_egger(correl = TRUE)` function implemented in `MendelianRandomization` package.

It was recently discovered that there was a minor bug in the `mr_egger(correl = TRUE)` function in which the signs from standardised instrument ratio estimates and correlation matrix are not aligned.

This bug was fixed in the `MendelianRandomization` version `0.4.1` and later, which is now set as the default of this workflow.
For backward reproducibility with earlier version before the fix was introduced,
these settings can be changed by modifying `MR_package_ver` in the `config.yaml` file to an earlier valid version of the package e.g. `0.4.0` (Note that this will force a system-wide installation of the specified version of the package).

### Configuring the workflow

As part of the multiverse sensitivity analysis, this workflow is designed to run up to 150 combinations of instrument selection parameters and MR models per protein.
To speed up this computation, the workflow is modularised so that protein-specific analysis can be run separately.
The target proteins for analysis can be configured by modifying the `target_protein_MR` list in the `config.yaml` file (default to all)

### Package dependencies
In addition to `conda`, `snakemake`, and `R`, this workflows uses the following `R` packages:
- [`tidyverse`](https://www.tidyverse.org/)
- [`data.table`](https://rdatatable.gitlab.io/data.table/)
- [`metafor`](https://cran.r-project.org/web/packages/metafor/index.html)
- [`MendelianRandomization`](https://cran.r-project.org/web/packages/MendelianRandomization/index.html)

If local installation of R and these packages are already present,
the workflow can be run using local system installation by ommiting the `--use-conda` argument, i.e.:

    snakemake -c all
