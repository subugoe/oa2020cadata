
## Research compendium for a dataset about corresponding author country affiliations indexed in the Web of Science 2014 - 2018

<!-- badges: start -->
[![Launch Rstudio Binder](http://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/subugoe/oa2020cadata/master?urlpath=rstudio)
[![Lifecycle: experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
[![Travis build status](https://travis-ci.org/subugoe/oa2020cadata.svg?branch=master)](https://travis-ci.org/subugoe/oa2020cadata)
<!-- badges: end -->

### Overview

This repository is a [research compendium](https://doi.org/10.7287/peerj.preprints.3192v2) providing a dataset about corresponding author country affiliations indexed in the Web of Science 2014 - 2018. The compendium contains data, code, and text associated with it. The R Markdown files in the [`analysis/`](analysis/) directory provide details about the data analysis, particularly about how the Web of Science in-house database from the [German Competence Center for Bibliometrics](http://www.bibliometrie.info/) was interfaced, as well as the [data descriptive](analysis/paper.md). The [`data/`](data/) directory contains all aggregated data. Because of the proprietary nature of the Web of Science, no raw data including access to the database can be shared. 

### Analysis files

The [`analysis/`](analysis/) directory contains the following reports written in [R Markdown](https://rmarkdown.rstudio.com/):

- [`paper.Rmd`](analysis/paper.Rmd) - The Data Descriptive including two use-cases. Link to [rendered report](analysis/paper.md)

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

This repository follow the structure of a [research compendium](https://doi.org/10.7287/peerj.preprints.3192v2) that uses R package structure to port data and code. 

#### Local use

Clone the GitHub repository with all data and code.

```
git clone https://github.com/subugoe/oa2020cadata.git
```

Open an R session in the directory of this package and install the R package dependencies using a package snapshot from the date this package was build

```r
devtools::install_deps(devtools::install_deps(repos = list(CRAN = 'http://mran.revolutionanalytics.com/snapshot/2019-09-08/')))
```

If you have access to the Competence Center of Bibliometrics data infrastructure, add your login credentials to your `.Renviron` file and save it.

```r
kb_user="najko"
kb_pwd="12345"
```

The Oracle database driver needed to access the remote database is included in this repository.

Documents are written in R Markdown and can be re-compiled with the `rmarkdown::render()`function.

#### Binder 


#### Limitations

### License

The compiled datasets are released into the public domain.

Anyone is free to copy, modify, publish, use, compile, sell, or distribute these materials in any form, for any purpose, commercial or non-commercial, and by any means.

Re-used data terms:

Crossref asserts no claims of ownership to individual items of bibliographic metadata and associated Digital Object Identifiers (DOIs) acquired through the use of the Crossref Free Services. Individual items of bibliographic metadata and associated DOIs may be cached and incorporated into the user's content and systems.

ISSN-Matching of Gold OA Journals (ISSN-GOLD-OA) 3.0 and Country Geocodes obtained from Google are made available under CC-BY.

Web of Science data © 2019 Clarivate Analytics. All rights reserved.


### Contributing

### Acknowledgment

This work is supported by the Federal Ministry of Education and Research of Germany (BMBF) in the framework Quantitative Research on the Science Sector (Project: "OAUNI Entwicklung und Einflussfaktoren des Open-Access-Publizierens an Universitäten in Deutschland", Förderkennzeichen: 01PU17023A).

### Contact

Najko Jahn, Data Analyst, SUB Göttingen. najko.jahn@sub.uni-goettingen.de





