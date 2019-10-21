Fetching Reprint Author information from the Web of Science
================

## About

Country information about corresponding authors play a crucial role in
open access funding (Schimmer, Geschuhn, and Vogler 2015). The Web of
Science contains data about this author role including affiliations,
making it a valuable source to determine publisher’s market shares per
country in terms of articles published.

In this work, a dataset is compiled providing information about
productivity of corresponding authors between 2014 - 2018. More
specificially, the following data points from the Web of of Science will
be investigated:

  - Web of Science collections SCI, SSCI and WOS.AHCI
  - Article types Original Articles and Reviews
  - Country of affiliations
  - ISSN
  - Publication year

This dataset will be extended with:

  - Publisher information from Crossref
  - Journal open access status provided by the ISSN GOLD OA list

In this document, every step needed to create the dataset is described.
This document is written in [RMarkdown](https://rmarkdown.rstudio.com/).
It can be re-compiled calling

``` r
require(rmarkdown)
require(tidyverse)
require(RJDBC)
require(rJava)
require(writexl)

rmarkdown::render("003_kb_fetch_ca.Rmd")
```

## Workflow

### Obtain publication data from the Web of Science database hosted by the German Competence Centre for Bibliometrics

#### Connect to database

``` r
# deal with rJava memory allocation 
# https://stackoverflow.com/questions/34624002/r-error-java-lang-outofmemoryerror-java-heap-space
options(java.parameters = "-Xmx1024m")
require(tidyverse)
require(RJDBC)
require(rJava)
.jinit()
jdbcDriver <-
  JDBC(driverClass = "oracle.jdbc.OracleDriver", classPath = "../inst/jdbc_driver/ojdbc8.jar")
jdbcConnection <-
  dbConnect(
    jdbcDriver,
    "jdbc:oracle:thin:@//biblio-p-db01:1521/bibliodb01.fiz.karlsruhe",
    Sys.getenv("kb_user"),
    Sys.getenv("kb_pwd")
  ) 
```

#### Query

``` sql
select
        wos_b_2019.d_items_authors_institutions.inst_countrycode,
        d.pubyear,
        d.issn,
        count(distinct(d.ut_eid)) as pubs
    from
        wos_b_2019.d_items_authors_institutions           
    inner join
        (
            select
                pk_items,
                fk_authors,
                ut_eid,
                wos_b_2019.items.pubyear,
                issn             
            from
                wos_b_2019.items                               
            inner join
                wos_b_2019.databasecollection                                                                     
                    on wos_b_2019.databasecollection.fk_items =  wos_b_2019.items.pk_items               
            inner join
                wos_b_2019.items_authors_institutions                                     
                    on wos_b_2019.items_authors_institutions.fk_items = wos_b_2019.items.pk_items             
            inner join
                wos_b_2019.issues                                   
                    on wos_b_2019.issues.pk_issues = wos_b_2019.items.fk_issues                
            where
                wos_b_2019.databasecollection.edition_value in (
                    'WOS.SCI', 'WOS.SSCI', 'WOS.AHCI'                                  
                )                                                             
                and wos_b_2019.items.doctype in (
                    'Article', 'Review'                                  
                )                                                              
                and wos_b_2019.items.pubyear in (
                    2014, 2015, 2016, 2017, 2018                                  
                )                                                         
                and wos_b_2019.items_authors_institutions.type = 'RP'                           
        ) d                           
            on (
                wos_b_2019.d_items_authors_institutions.fk_items = d.pk_items                                   
                and wos_b_2019.d_items_authors_institutions.fk_authors = d.fk_authors                          
            )               
    where
        wos_b_2019.d_items_authors_institutions.inst_countrycode is not null     
    group by
        wos_b_2019.d_items_authors_institutions.inst_countrycode,
        d.pubyear,
        d.issn
```

``` r
# dump
write_csv(rp_country, "../data/rp_data_14_18.csv")
```

### Get most frequent publisher and journal names from Crossref

Publisher names used in the Web of Science are ambigue, and sometimes
journals can change the publisher. To obtain most frequent publisher and
journals names, Crossref was queried for every distinct journal by ISSN
as described in [`fetch_data.R`](fetch_data.R).

As a result, a [matching table between Crossref and the Web of Science
journals](data/cr_wos.csv) was created. In the following, this table is
joined with the WoS data.

``` r
cr_journals <- readr::read_csv("../data/cr_wos.csv") %>%
  # merge springer nature brands
  mutate(publisher = ifelse(grepl("Springer", publisher, fixed = FALSE), "Springer Nature", publisher))
rp_country <- readr::read_csv("../data/rp_data_14_18.csv")
issn_l <- readr::read_tsv("../data/20190818.ISSN-to-ISSN-L.txt") %>%
  # manual fix Journal - American Water Works 
  add_row(ISSN = "2164-4535", `ISSN-L` = "0003-150X")
rp_df <- rp_country %>%
  left_join(issn_l, by = "ISSN") %>%
  left_join(cr_journals, by = c(ISSN = "issn")) %>%
  distinct()
```

Crossref indexed 90.5184969 % of Web of Science journals.

The following table shows the number of journals per publisher.

``` r
rp_df %>% 
  distinct(ISSN, publisher) %>% 
  count(publisher, sort = TRUE) %>% 
  mutate(prop = n /sum(n))
```

    ## # A tibble: 1,186 x 3
    ##    publisher                                     n   prop
    ##    <chr>                                     <int>  <dbl>
    ##  1 Elsevier BV                                1862 0.136 
    ##  2 Springer Nature                            1813 0.132 
    ##  3 Informa UK Limited                         1393 0.101 
    ##  4 <NA>                                       1302 0.0948
    ##  5 Wiley                                      1292 0.0941
    ##  6 SAGE Publications                           661 0.0481
    ##  7 Oxford University Press (OUP)               328 0.0239
    ##  8 Cambridge University Press (CUP)            301 0.0219
    ##  9 Ovid Technologies (Wolters Kluwer Health)   213 0.0155
    ## 10 Walter de Gruyter GmbH                      201 0.0146
    ## # … with 1,176 more rows

### 3\. Obtain open access status information

Bruns et al. provide a matching table for fully open access journals,
joining multiple sources into one dataset.

Bruns, A., Lenke, C., Schmidt, C., & Taubert, N. C. (2019).
ISSN-Matching of Gold OA Journals (ISSN-GOLD-OA) 3.0. Bielefeld
University.
[doi:\[10.4119/unibi/2934907\](https://doi.org/10.4119/unibi/2934907)](doi:%5B10.4119/unibi/2934907%5D\(https://doi.org/10.4119/unibi/2934907\))

The linking ISSN is used for
matching.

``` r
u <- "https://pub.uni-bielefeld.de/download/2934907/2934908/ISSN_Gold-OA_3.0.csv"
bie_oa <- readr::read_csv(u) %>%
  # remove missing entries with missing ISSN_L
  filter(!is.na(ISSN_L))
# add info to rp_df
rp_df_oa <- rp_df %>%
  mutate(oa_journal = `ISSN-L` %in% bie_oa$`ISSN_L`)
```

The following table shows the number and proportion of open access
journals

``` r
rp_df_oa %>% 
  distinct(`ISSN-L`, oa_journal) %>% 
  count(oa_journal) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   oa_journal     n  prop
    ##   <lgl>      <int> <dbl>
    ## 1 FALSE      11580 0.854
    ## 2 TRUE        1981 0.146

### 4\. Data coding and dump journal-level

`CSV`-based dump

``` r
rp_df_oa %>%
  select(
    issn_wos = ISSN,
    country_code = INST_COUNTRYCODE,
    publication_year = PUBYEAR,
    articles = PUBS,
    journal_title, publisher, oa_journal,
    issn_l = `ISSN-L`) %>%
  write_csv("../data/rp_jn_14_18.csv")
```

Excel dump. To avoid loading issues because of too many rows in one
sheet, split the dataset into several sheets per year.

``` r
require(writexl)
rp_df <- readr::read_csv("../data/rp_jn_14_18.csv")
tmp <- map(2014:2018, function(x) {
  rp_df %>% 
    filter(publication_year == x)
  }
)
# names
names(tmp) <- paste0("pubyear_", 2014:2018)
write_xlsx(tmp, "../data/rp_jn_14_18.xlsx")
```

Data
Schema:

| Variable           | Description                                                                                                                                                                      | Source                                                                                                                                                     |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `issn_wos`         | ISSN, a standardized journal id.                                                                                                                                                 | KB Web of Science: `wos_b_2019.issues.issn`                                                                                                                |
| `country_code`     | Country of affiliation corresponding author, represented as ISO 3 code                                                                                                           | KB Web of Science: `wos_b_2019.d_items_authors_institutions.inst_countrycode`                                                                              |
| `publication_year` | Year of publication, obtained from KB Web of Science                                                                                                                             | KB Web of Science: `wos_b_2019.items.pubyear`                                                                                                              |
| `articles`         | Number of original articles and reviews published. Whole counting where internationally co-located corresponding authorships were assigned to each contributing country equally. | KB Web of Science: Grouped counts over `wos_b_2019.issues.issn`, `wos_b_2019.d_items_authors_institutions.inst_countrycode` and `wos_b_2019.items.pubyear` |
| `journal_title`    | Most frequently used journal title in terms of articles published between 2014 - 2018. If missing, the journal was not indexed in Crossref                                       | Crossref                                                                                                                                                   |
| `publisher`        | Most frequently used publisher name in terms of articles published between 2014 - 2018. If missing, the journal was not indexed in Crossref                                      | Crossref                                                                                                                                                   |
| `oa_journal`       | Is the journal publishing all articles open access without delay (full open access)?                                                                                             | Bielefeld GOLD OA List V3                                                                                                                                  |
| `issn_l`           | Linking ISSN, a journal id that groups the different media of the same serial publication, e.g. ISSN for print with electronic issn.                                             | CIEPS                                                                                                                                                      |

### 5\. Publisher-level data

``` r
rp_jn <- readr::read_csv("../data/rp_jn_14_18.csv") %>%
  group_by(country_code, publication_year, publisher, oa_journal) %>%
  summarise(n_publications = sum(articles, na.rm = TRUE),
            n_journals = n_distinct(issn_l))
# export to csv and excel
readr::write_csv(rp_jn, "../data/rp_publisher_14_18.csv")
writexl::write_xlsx(rp_jn, "../data/rp_publisher_14_18.xlsx")
```

Data
Schema:

| Variable           | Description                                                                                                                                 | Source                                                                        |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------- |
| `country_code`     | Country of affiliation corresponding author, represented as ISO 3 code                                                                      | KB Web of Science: `wos_b_2019.d_items_authors_institutions.inst_countrycode` |
| `publication_year` | Year of publication, obtained from KB Web of Science                                                                                        | KB Web of Science: `wos_b_2019.items.pubyear`                                 |
| `publisher`        | Most frequently used publisher name in terms of articles published between 2014 - 2018. If missing, the journal was not indexed in Crossref | Crossref                                                                      |
| `oa_journal`       | Is the journal publishing all articles open access without delay (full open access)?                                                        | ISSN GOLD OA List V3                                                          |
| `n_publications`   | Number of original articles and reviews published                                                                                           | Aggregate data                                                                |
| `n_journals`       | Number of distinct journals with corresponding publication output                                                                           | Aggregate data                                                                |
