
## Research compendium for a dataset about corresponding author country affiliations indexed in the Web of Science 2014 - 2018

<!-- badges: start -->
[![Launch Rstudio Binder](http://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/subugoe/oa2020cadata/master?urlpath=rstudio)
[![Lifecycle: experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
[![Travis build status](https://travis-ci.org/subugoe/oa2020cadata.svg?branch=master)](https://travis-ci.org/subugoe/oa2020cadata)
<!-- badges: end -->

### Overview

This repository is a [research compendium](https://doi.org/10.7287/peerj.preprints.3192v2) providing a dataset that covers corresponding author country affiliations indexed in the Web of Science 2014 - 2018. The compendium contains data, code, and text associated with it. The R Markdown files in the [`analysis/`](analysis/) directory contain details about the data analysis, particularly about how the Web of Science in-house database from the [German Competence Center for Bibliometrics](http://www.bibliometrie.info/) was interfaced, as well as the [data descriptive](analysis/paper.md). The [`data/`](data/) directory contains all aggregated data. Because of the proprietary nature of the Web of Science, no raw data including access to the database can be shared. 

### Analysis files

The [`analysis/`](analysis/) directory contains the following reports written in [R Markdown](https://rmarkdown.rstudio.com/):

- [`paper.Rmd`](analysis/paper.Rmd) - The Data Descriptive including two use-cases. Link to [rendered report](analysis/paper.Rmd)

Analytical steps for obtaining the data from the Web of Science in-house database maintained by the German Competence Center for Bibliometrics (WoS-KB), and data enriching were also provided as R Markdown reports:

- [`001_kb_rp_pretest.Rmd`](analysis/001_kb_rp_pretest.Rmd) - Initial exploration about how corresponding authorships are represented in the Wos_KB. Link to [rendered report](analysis/001_kb_rp_pretest.md)
- [`002_kb_rp_coverage.Rmd`](analysis/002_kb_rp_coverage.Rmd) - Coverage analysis of reprint authors in the WoS-KB for the period 2014 - 2018. Link to [rendered report](analysis/002_kb_rp_coverage.md)
- [`003_kb_fetch_ca.Rmd`](analysis/003_kb_fetch_ca.Rmd) - Obtain and compile the dataset about corresponding author country affiliations 2014 - 2018. Link to [rendered report](analysis/003_kb_fetch_ca.md)
- [`004_kb_fetch_publisher.Rmd`](analysis/004_kb_fetch_publisher.Rmd) - Obtain and compile the dataset about the global publisher output 2014 - 2018. Link to [rendered report](analysis/004_kb_fetch_publisher.md)

### Data files

The [`data/`](data/) directory contains the resulting datasets stored as comma-separated value files.

- [`rp_data_14_18.csv`](data/rp_data_14_18.csv) - Corresponding author country affiliations per publisher, journal and open access publishing model 2014 - 2018
- [`rp_jn_14_18.csv`](data/rp_jn_14_18.csv) - Corresponding author country affiliations per publisher and journal as provided by the WoS-KB
- [`publisher_14_18.csv`](data/publisher_14_18.csv) - Global publisher and journal output 2014 - 2018 
- [`cr_wos.csv`](data/cr_wos.csv) - Matching table publisher and journal infos Crossref / Web of Science
- [`20190818.ISSN-to-ISSN-L.txt`](data/20190818.ISSN-to-ISSN-L.txt) - ISSN matching table from <https://www.issn.org/understanding-the-issn/assignment-rules/the-issn-l-for-publications-on-multiple-media/>
- [`geocodes.csv`](data/geocodes.csv) - Country geocodes obtained from <https://developers.google.com/public-data/docs/canonical/countries_csv>

### Reproducibility notes

### Acknowledgment

This work is supported by the Federal Ministry of Education and Research of Germany (BMBF) in the framework Quantitative Research on the Science Sector (Project: "OAUNI Entwicklung und Einflussfaktoren des Open-Access-Publizierens an Universitäten in Deutschland", Förderkennzeichen: 01PU17023A).

### Contact

Najko Jahn, Data Analyst, SUB Göttingen. najko.jahn@sub.uni-goettingen.de





