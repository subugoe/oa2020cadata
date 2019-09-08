
## Research compendium for a dataset about corresponding author country affiliations indexed in the Web of Science 2014 - 2018

<!-- badges: start -->
[![Launch Rstudio Binder](http://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/subugoe/oa2020cadata/master?urlpath=rstudio)
<!-- badges: end -->

### Overview

This repository is a [research compendium](https://doi.org/10.7287/peerj.preprints.3192v2) for generating a dataset about corresponding author country affiliations indexed in the Web of Science 2014 - 2018. The compendium contains all data, code, and text associated with it. The R Markdown files in the `analysis/` directory contain details of the data analysis, particularly about how the Web of Science in-house database from the German Competence Center for Bibliometrics was interface. The `data/` directory contains all aggregated data. Due to the proprietary nature of data from the Web of Science, only aggregated counts were shared for research purposes. 

### Analysis files

The `analysis` directory contains the following report written in R Markdown:

- `paper.Rmd` - The Data Descriptive including use-cases

Analytical steps for obtaining the data from the Web of Science in-house database (WoS-KB) from the German Competence Center for Bibliometrics and enriching were also written in R Markdown:

- `001_kb_rp_pretest.Rmd` - Initial exploration about how corresponding authorships are represented in the Wos_KB
- `002_kb_rp_coverage.Rmd` - Coverage analysis of reprint authors in the WoS-KB for the period 2014 - 2018
- `003_kb_fetch_ca.Rmd` - Obtain and compile dataset about corresponding author country affiliations 2014 - 2018
- `004_kb_fetch_publisher.Rmd` - Obtain and compile dataset about the global publisher output 2014 - 2018

### Data files

The `data/` directory contains the resulting datasets stored as comma-separated value files

- `rp_data_14_18.csv` - Corresponding author country affiliations per publisher, journal and open access publishing model 2014 - 2018
- `rp_jn_14_18.csv` - Corresponding author country affiliations per publisher and journal as provided by the WoS-KB
- `publisher_14_18.csv` - Gloabl publisher and journal output 2014 - 2018 
- `cr_wos.csv` - Matching table publisher and journal infos Crossref / Web of Science

### Reproducibility notes

### Contact

Najko Jahn, Data Analyst, SUB Göttingen. najko.jahn@sub.uni-goettingen.de





