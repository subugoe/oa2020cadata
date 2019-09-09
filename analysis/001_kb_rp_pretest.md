Reprint Authors per Country
================

    #> [1] 0

## Motivation

Country information about corresponding authors play a crucial role in
open access funding (Schimmer, Geschuhn, and Vogler 2015). The Web of
Science contains data about this author role including affiliations,
making it a valuable source to determine the productivity of
corresponding authors per country ad journal.

The aim of this work is to better understand how countries of
affiliation from corresponding authors are represented in the WoS-KB,
and how they can be retrieved from it. Such an analysis will yield
valuable insights about how to calculate the number of scholarly
articles per country and journal from the WoS-KB database using the
corresponding author addresses only.

## Potential issues

1.  A corresponding author lists at least two different countries
2.  More than one corresponding author is listed
3.  Corresponding authors work in different countries

## Example 1

A corresponding author lists at least two different countries, e.g.,
`UT=000400754000138`

``` sql
select  distinct wos_b_2019.institutions.countrycode,
        wos_b_2019.items_authors_institutions.type,
        wos_b_2019.items_authors_institutions.fk_authors,
        wos_b_2019.items.ut_eid,
        wos_b_2019.items.pubyear
from wos_b_2019.items
inner join
        wos_b_2019.items_authors_institutions                                 
            on wos_b_2019.items_authors_institutions.fk_items = wos_b_2019.items.pk_items       
inner join
        wos_b_2019.institutions                          
            on wos_b_2019.institutions.pk_institutions = wos_b_2019.items_authors_institutions.fk_institutions  
where wos_b_2019.items.ut_eid = '000400754000138'
order by wos_b_2019.items_authors_institutions.fk_authors
```

| COUNTRYCODE | TYPE | FK\_AUTHORS | UT\_EID         | PUBYEAR |
| :---------- | :--- | ----------: | :-------------- | ------: |
| ARE         | RS   |     6856105 | 000400754000138 |    2017 |
| DEU         | RS   |     6856105 | 000400754000138 |    2017 |
| DEU         | RP   |     6983964 | 000400754000138 |    2017 |
| GBR         | RS   |     6983964 | 000400754000138 |    2017 |
| DEU         | RS   |     6983964 | 000400754000138 |    2017 |

5 records

Potential issues: Only one affiliation is tagged as `RP` in
`wos_b_2019.items_author_institutions`.

## Example 2

More than one corresponding author is listed. For
`UT_EID=000372645900002`, the Web of Science lists seven corresponding
authors and four reprint adresses.

``` sql
select  distinct wos_b_2019.institutions.countrycode,
        wos_b_2019.items_authors_institutions.type,
        wos_b_2019.items_authors_institutions.fk_authors,
        wos_b_2019.items.ut_eid,
        wos_b_2019.items.pubyear
from wos_b_2019.items
inner join
        wos_b_2019.items_authors_institutions                                 
            on wos_b_2019.items_authors_institutions.fk_items = wos_b_2019.items.pk_items       
inner join
        wos_b_2019.institutions                          
            on wos_b_2019.institutions.pk_institutions = wos_b_2019.items_authors_institutions.fk_institutions  
where wos_b_2019.items.ut_eid = '000372645900002'
order by wos_b_2019.items_authors_institutions.fk_authors
```

| COUNTRYCODE | TYPE | FK\_AUTHORS | UT\_EID         | PUBYEAR |
| :---------- | :--- | ----------: | :-------------- | ------: |
| FIN         | RP   |     3366141 | 000372645900002 |    2016 |
| FIN         | RS   |     3366141 | 000372645900002 |    2016 |
| FIN         | RS   |    16110610 | 000372645900002 |    2016 |
| FIN         | RP   |    16110610 | 000372645900002 |    2016 |
| FIN         | RS   |    20814470 | 000372645900002 |    2016 |
| FIN         | RP   |    20814470 | 000372645900002 |    2016 |
| FIN         | RS   |    20985641 | 000372645900002 |    2016 |
| FIN         | RP   |    20985641 | 000372645900002 |    2016 |
| GBR         | RS   |    23625968 | 000372645900002 |    2016 |
| FIN         | RP   |    23625968 | 000372645900002 |    2016 |
| FIN         | RP   |    25524442 | 000372645900002 |    2016 |
| GBR         | RS   |    25524442 | 000372645900002 |    2016 |
| FIN         | RP   |    26699265 | 000372645900002 |    2016 |
| FIN         | RS   |    26699265 | 000372645900002 |    2016 |

14 records

Potential issue: As in Example 1, Only one affiliation is tagged as `RP`
in `wos_b_2019.items_author_institutions`.

## Example 3

Corresponding authors work in different countries. For
`UT_EID=000372645900002`, the Web of Science lists two corresponding
authors, one affilated with a Spanish, the other with a Portuguese
institution.

``` sql
select  distinct wos_b_2019.institutions.countrycode,
        wos_b_2019.items_authors_institutions.type,
        wos_b_2019.items_authors_institutions.fk_authors,
        wos_b_2019.items.ut_eid,
        wos_b_2019.items.pubyear
from wos_b_2019.items
inner join
        wos_b_2019.items_authors_institutions                                 
            on wos_b_2019.items_authors_institutions.fk_items = wos_b_2019.items.pk_items       
inner join
        wos_b_2019.institutions                          
            on wos_b_2019.institutions.pk_institutions = wos_b_2019.items_authors_institutions.fk_institutions  
where wos_b_2019.items.ut_eid = '000389110200022'
order by wos_b_2019.items_authors_institutions.fk_authors
```

| COUNTRYCODE | TYPE | FK\_AUTHORS | UT\_EID         | PUBYEAR |
| :---------- | :--- | ----------: | :-------------- | ------: |
| ESP         | RS   |     4888789 | 000389110200022 |    2017 |
| ESP         | RS   |    12292345 | 000389110200022 |    2017 |
| ESP         | RS   |    12696571 | 000389110200022 |    2017 |
| ESP         | RS   |    13655258 | 000389110200022 |    2017 |
| PRT         | RP   |    15145104 | 000389110200022 |    2017 |
| ESP         | RS   |    15145104 | 000389110200022 |    2017 |
| ESP         | RS   |    18906313 | 000389110200022 |    2017 |
| ESP         | RS   |    25489070 | 000389110200022 |    2017 |
| PRT         | RS   |    27951198 | 000389110200022 |    2017 |
| PRT         | RP   |    27951198 | 000389110200022 |    2017 |

Displaying records 1 - 10

Potential issues: As in Example 1 and 2, only one affiliation is tagged
with RP in `wos_b_2019.items_author_institutions`. Moreover, checking
the WoS raw data reveals that the RP-author `14734495` is not affiliated
with an Portuguese institution at all.

## SQL strategy to circumvent these issues

### Steps

1.  Get all `FK_AUTHORS` tagged as `RP`, and store the result
    `rp_author_table`
2.  From authors listed in `rp_author_table` obtain all countries of
    affilation and store them in `rp_countries_table`
3.  Aggregate
`rp_countries_table`

#### 1\. Get all `FK_AUTHORS` tagged as `RP`, and store the result `rp_author_table`

``` sql
select
        distinct wos_b_2019.items_authors_institutions.fk_items,
        wos_b_2019.items_authors_institutions.fk_authors      
    from
        wos_b_2019.items      
    inner join
        wos_b_2019.databasecollection              
            on wos_b_2019.databasecollection.fk_items =  wos_b_2019.items.pk_items        
    inner join
        wos_b_2019.items_authors_institutions                                   
            on wos_b_2019.items_authors_institutions.fk_items = wos_b_2019.items.pk_items       
    inner join
        wos_b_2019.institutions                          
            on wos_b_2019.institutions.pk_institutions = wos_b_2019.items_authors_institutions.fk_institutions      
    where
        wos_b_2019.databasecollection.edition_value in ('WOS.SCI', 'WOS.SSCI', 'WOS.AHCI')                  
        and wos_b_2019.items.doctype in ('Article', 'Review')                   
        and wos_b_2019.items.pubyear in (2014, 2015, 2016, 2017)         
        and wos_b_2019.items.ut_eid in ('000389110200022', '000372645900002', '000400754000138') 
        and wos_b_2019.items_authors_institutions.type = 'RP'
```

Store in personal table space

``` r
dbWriteTable(conn = jdbcConnection, 
             name = "rp_author_table", 
             value = rp_authors)
#> [1] TRUE
```

#### From authors listed in `rp_author_table` obtain all countries of affilation and store them in `rp_countries_table`

``` sql
select
        countrycode,
        wos_b_2019.items_authors_institutions.type,
        wos_b_2019.items_authors_institutions.fk_institutions,
        wos_b_2019.items_authors_institutions.fk_authors,
        wos_b_2019.issues.issn,
        ut_eid,
        wos_b_2019.items.pubyear
    from
        wos_b_2019.items      
    inner join
        wos_b_2019.issues                           
            on wos_b_2019.issues.pk_issues = wos_b_2019.items.fk_issues         
    inner join
        wos_b_2019.items_authors_institutions                                        
            on wos_b_2019.items_authors_institutions.fk_items = wos_b_2019.items.pk_items       
    inner join
        wos_b_2019.institutions                          
            on wos_b_2019.institutions.pk_institutions = wos_b_2019.items_authors_institutions.fk_institutions      
    inner join
        rp_author_table                          
            on (
                rp_author_table.fk_items = wos_b_2019.items_authors_institutions.fk_items                  
                and rp_author_table.fk_authors = wos_b_2019.items_authors_institutions.fk_authors             
            )
```

Store in personal table space

``` r
dbWriteTable(conn = jdbcConnection, 
             name = "rp_countries_table", 
             value = rp_countries)
#> [1] TRUE
```

#### Aggregate `rp_countries_table`

Counting method: country of affilation is only counted once per article.

``` sql
select
    countrycode,
    issn,
    count(distinct(ut_eid)) as publications 
from
    rp_countries_table 
group by
    countrycode,
    issn
order by issn
```

| COUNTRYCODE | ISSN      | PUBLICATIONS |
| :---------- | :-------- | -----------: |
| ESP         | 0212-9728 |            1 |
| PRT         | 0212-9728 |            1 |
| FIN         | 0278-0046 |            1 |
| GBR         | 0278-0046 |            1 |
| GBR         | 1432-0746 |            1 |
| DEU         | 1432-0746 |            1 |

6 records

<div id="refs" class="references">

<div id="ref-Schimmer_2015">

Schimmer, Ralf, Kai Geschuhn, and Andreas Vogler. 2015. “Disrupting the
subscription journals’business model for the necessary large-scale
transformation to open access.” Max Planck Digital Library.
<https://doi.org/10.17617/1.3>.

</div>

</div>
