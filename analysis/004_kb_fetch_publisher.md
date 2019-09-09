Publisher League Global
================

## About

This executable report describes how data about the global market shares
of publishers were retrieved from the Web of Science (WoS) in-house
database hosted by the [German Competence Center for Bibliometrics
(KB)](http://www.bibliometrie.info/). This document is written in R
Markdown. If you have access to WoS-KB data infrastructure, you will be
able to replicate the methods used by calling

``` r
require(rmarkdown)
require(tidyverse)
require(RJDBC)
require(rJava)
require(writexl)
rmarkdown::render("004_kb_fetch_publisher_global.Rmd")
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
  JDBC(driverClass = "oracle.jdbc.OracleDriver", classPath = "../inst/jdbc_driver/ojdbc8.jar") # you may need to change the path to your db driver
jdbcConnection <-
  dbConnect(
    jdbcDriver,
    "jdbc:oracle:thin:@//biblio-p-db01:1521/bibliodb01.fiz.karlsruhe",
    # login credentials are stored in the .Rprofile file 
    Sys.getenv("kb_user"), 
    Sys.getenv("kb_pwd")
  ) 
```

#### Query

``` sql
select
        wos_b_2019.items.pubyear,
        wos_b_2019.issues.issn,
        count(distinct(ut_eid)) as pubs                         
    from
        wos_b_2019.items                                            
    inner join
        wos_b_2019.databasecollection                                                                                          
            on wos_b_2019.databasecollection.fk_items =  wos_b_2019.items.pk_items                                  
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
    group by
        wos_b_2019.items.pubyear,
        wos_b_2019.issues.issn
```

``` r
head(rp_publisher) %>%
  knitr::kable()
```

| PUBYEAR | ISSN      | PUBS |
| ------: | :-------- | ---: |
|    2016 | 0045-6535 | 1645 |
|    2018 | 0013-4651 | 1527 |
|    2017 | 0016-5107 |  223 |
|    2018 | 0167-4544 |  418 |
|    2016 | 2163-0755 |  310 |
|    2018 | 0736-4679 |  325 |

``` r
# dump
write_csv(rp_publisher, "../data/publisher_league_14_18.csv")
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
rp_publisher <- readr::read_csv("../data/publisher_league_14_18.csv")
issn_l <- readr::read_tsv("../data/20190818.ISSN-to-ISSN-L.txt") %>%
  # manual fix Journal - American Water Works 
  add_row(ISSN = "2164-4535", `ISSN-L` = "0003-150X")
rp_df <- rp_publisher %>%
  left_join(issn_l, by = "ISSN") %>%
  left_join(cr_journals, by = c(`ISSN-L`= "issn_l")) %>%
  select(-issn) %>%
  distinct()
```

Crossref indexed 88.18 % of Web of Science journals.

The following table shows the number of journals per publisher.

``` r
rp_df %>% 
  distinct(ISSN, publisher) %>% 
  count(publisher, sort = TRUE) %>% 
  mutate(prop = n /sum(n) * 100)
```

    ## # A tibble: 1,166 x 3
    ##    publisher                                     n  prop
    ##    <chr>                                     <int> <dbl>
    ##  1 Elsevier BV                                1807 13.1 
    ##  2 Springer Nature                            1782 12.9 
    ##  3 <NA>                                       1627 11.8 
    ##  4 Informa UK Limited                         1362  9.90
    ##  5 Wiley                                      1277  9.28
    ##  6 SAGE Publications                           652  4.74
    ##  7 Oxford University Press (OUP)               319  2.32
    ##  8 Cambridge University Press (CUP)            292  2.12
    ##  9 Ovid Technologies (Wolters Kluwer Health)   210  1.53
    ## 10 Walter de Gruyter GmbH                      199  1.45
    ## # … with 1,156 more rows

Next, the number and proportion of original articles and reviews
published per publisher is presented:

``` r
rp_df %>% 
  group_by(publisher) %>%
  summarise(n = sum(PUBS)) %>% 
  mutate(prop = n /sum(n) * 100) %>%
  arrange(desc(prop))
```

    ## # A tibble: 1,166 x 3
    ##    publisher                                                      n  prop
    ##    <chr>                                                      <dbl> <dbl>
    ##  1 Elsevier BV                                              1942425 23.8 
    ##  2 Springer Nature                                          1134385 13.9 
    ##  3 Wiley                                                     711824  8.73
    ##  4 Informa UK Limited                                        411910  5.05
    ##  5 <NA>                                                      395542  4.85
    ##  6 American Chemical Society (ACS)                           216320  2.65
    ##  7 SAGE Publications                                         190534  2.34
    ##  8 Royal Society of Chemistry (RSC)                          189477  2.32
    ##  9 Institute of Electrical and Electronics Engineers (IEEE)  178400  2.19
    ## 10 Oxford University Press (OUP)                             176699  2.17
    ## # … with 1,156 more rows

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
    ## 1 FALSE      11609 0.854
    ## 2 TRUE        1981 0.146

### 4\. Data coding and dump

`CSV`-based dump

``` r
rp_df_oa %>%
  select(
    issn_wos = ISSN,
    publication_year = PUBYEAR,
    articles = PUBS,
    journal_title, publisher, oa_journal,
    issn_l = `ISSN-L`
    ) %>%
  write_csv("../data/publisher_14_18.csv")
```

Dump to Excel

``` r
readr::read_csv("../data/publisher_14_18.csv") %>%
  writexl::write_xlsx("../data/publisher_14_18.xlsx")
```

Data Schema:

Data
Schema:

| Variable           | Description                                                                                                                                 | Source                                                                                         |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------- |
| `issn_wos`         | ISSN, a standardized journal id.                                                                                                            | KB Web of Science: `wos_b_2019.issues.issn`                                                    |
| `publication_year` | Year of publication, obtained from KB Web of Science                                                                                        | KB Web of Science: `wos_b_2019.items.pubyear`                                                  |
| `articles`         | Number of original articles and reviews published.                                                                                          | KB Web of Science: Grouped counts over `wos_b_2019.issues.issn` and `wos_b_2019.items.pubyear` |
| `journal_title`    | Most frequently used journal title in terms of articles published between 2014 - 2018. If missing, the journal was not indexed in Crossref  | Crossref                                                                                       |
| `publisher`        | Most frequently used publisher name in terms of articles published between 2014 - 2018. If missing, the journal was not indexed in Crossref | Crossref                                                                                       |
| `oa_journal`       | Is the journal publishing all articles open access without delay (full open access)?                                                        | Bielefeld GOLD OA List V3                                                                      |
| `issn_l`           | Linking ISSN, a journal id that groups the different media of the same serial publication, e.g. ISSN for print with electronic issn.        | CIEPS                                                                                          |
