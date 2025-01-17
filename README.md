rfishbase Biological Table
================

-   [Get taxonomy info](#get-taxonomy-info)
-   [Get Lmax for all fish](#get-lmax-for-all-fish)
-   [Take aggregate means and add it to the lmax
    table](#take-aggregate-means-and-add-it-to-the-lmax-table)
-   [a/b table](#ab-table)
-   [Combine lmax and a/b tables](#combine-lmax-and-ab-tables)
-   [Trophic levels](#trophic-levels)
-   [Join trophic table with lmax/ab](#join-trophic-table-with-lmaxab)
    -   [Update data.world file](#update-data.world-file)

In this notebook, we will compile a table of lmax and a/b coefficients
using data from fishbase.

### Get taxonomy info

``` r
taxa <- rfishbase::load_taxa(server = "fishbase") %>% 
  dplyr::select(SpecCode, Species, Genus, Family) %>% 
  dplyr::mutate(server = "fishbase") %>% 
  dplyr::bind_rows(
    rfishbase::load_taxa(server = "sealifebase") %>% 
      dplyr::select(SpecCode, Species, Genus, Family) %>% 
      dplyr::mutate(server = "sealifebase")
  )
```

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10003 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10003 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10003 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10003 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10001 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10001 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10003 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10001 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10006 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

### Get Lmax for all fish

Many of these will be missing and we will fill them in later by taking
means across genus and families.

``` r
suppressWarnings({
  lmax_info <- rfishbase::popchar(server = "fishbase") %>% 
    dplyr::select(Species, Lmax) %>% 
    dplyr::bind_rows(
      rfishbase::popchar(server = "sealifebase") %>% 
        dplyr::select(Species, Lmax)
    ) %>% 
    dplyr::mutate(lmax = as.numeric(Lmax)) %>% 
    dplyr::group_by(Species) %>% 
    dplyr::summarize(Lmax = max(lmax, na.rm = TRUE)) %>%
    dplyr::mutate(Lmax = ifelse(is.infinite(Lmax), NA, Lmax)) %>% 
    dplyr::left_join(taxa, by = "Species")
})
lmax_info
```

    ## # A tibble: 131,941 x 6
    ##    Species                 Lmax SpecCode Genus           Family        server   
    ##    <chr>                  <dbl>    <int> <chr>           <chr>         <chr>    
    ##  1 Aapticheilichthys web…    NA    64588 Aapticheilicht… Procatopodid… fishbase 
    ##  2 Aaptolasma americana      NA    32307 Aaptolasma      Bathylasmati… sealifeb…
    ##  3 Aaptolasma brintoni       NA    32306 Aaptolasma      Bathylasmati… sealifeb…
    ##  4 Aaptolasma callistode…    NA    32308 Aaptolasma      Bathylasmati… sealifeb…
    ##  5 Aaptolasma leptoderma     NA    32304 Aaptolasma      Bathylasmati… sealifeb…
    ##  6 Aaptolasma triderma       NA    32305 Aaptolasma      Bathylasmati… sealifeb…
    ##  7 Aaptos aaptos             NA    51720 Aaptos          Suberitidae   sealifeb…
    ##  8 Aaptos bergmanni          NA   165941 Aaptos          Suberitidae   sealifeb…
    ##  9 Aaptos ciliata            NA   105687 Aaptos          Suberitidae   sealifeb…
    ## 10 Aaptos duchassaingi       NA   139407 Aaptos          Suberitidae   sealifeb…
    ## # … with 131,931 more rows

### Take aggregate means and add it to the lmax table

``` r
genus_Lmax_info <- lmax_info %>% 
  dplyr::group_by(Genus) %>% 
  dplyr::summarize(genus_Lmax = mean(Lmax, na.rm = TRUE))

family_Lmax_info <- lmax_info %>% 
  dplyr::group_by(Family) %>% 
  dplyr::summarize(family_Lmax = mean(Lmax, na.rm = TRUE))

lmax_table <- lmax_info %>% 
  dplyr::rename(species_Lmax = Lmax) %>% 
  dplyr::left_join(genus_Lmax_info, by = "Genus") %>% 
  dplyr::left_join(family_Lmax_info, by = "Family")

# Fill in missing data
bio_table <- lmax_table %>% 
  dplyr::mutate(
    lmax = ifelse(is.na(species_Lmax),
      ifelse(is.na(genus_Lmax),
        family_Lmax,
        genus_Lmax
      ),
      species_Lmax
    )
  ) %>% dplyr::select(
    SpecCode, Species, Genus, Family, lmax, server
  )

bio_table
```

    ## # A tibble: 131,941 x 6
    ##    SpecCode Species                Genus           Family         lmax server   
    ##       <int> <chr>                  <chr>           <chr>         <dbl> <chr>    
    ##  1    64588 Aapticheilichthys web… Aapticheilicht… Procatopodi…   3.30 fishbase 
    ##  2    32307 Aaptolasma americana   Aaptolasma      Bathylasmat… NaN    sealifeb…
    ##  3    32306 Aaptolasma brintoni    Aaptolasma      Bathylasmat… NaN    sealifeb…
    ##  4    32308 Aaptolasma callistode… Aaptolasma      Bathylasmat… NaN    sealifeb…
    ##  5    32304 Aaptolasma leptoderma  Aaptolasma      Bathylasmat… NaN    sealifeb…
    ##  6    32305 Aaptolasma triderma    Aaptolasma      Bathylasmat… NaN    sealifeb…
    ##  7    51720 Aaptos aaptos          Aaptos          Suberitidae  NaN    sealifeb…
    ##  8   165941 Aaptos bergmanni       Aaptos          Suberitidae  NaN    sealifeb…
    ##  9   105687 Aaptos ciliata         Aaptos          Suberitidae  NaN    sealifeb…
    ## 10   139407 Aaptos duchassaingi    Aaptos          Suberitidae  NaN    sealifeb…
    ## # … with 131,931 more rows

### a/b table

Now we do the same process but for a/b coefficients. For this table, we
only aggregate up to the genus.

``` r
ab_info <- rfishbase::poplw(server = "fishbase") %>% 
  dplyr::select(Species, a, b) %>% 
  dplyr::bind_rows(
    rfishbase::poplw(server = "sealifebase") %>% 
      dplyr::select(Species, a, b)
  ) %>% 
  # Some species have multiple a/b values so take the mean
  dplyr::group_by(Species) %>% 
  dplyr::summarize(
    a = mean(a, na.rm = TRUE),
    b = mean(b, na.rm = TRUE)
  ) %>% 
  dplyr::left_join(taxa, by = "Species")
```

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10003 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10001 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10000 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10003 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10003 ms

``` r
genus_ab_info <- ab_info %>% 
  dplyr::group_by(Genus) %>% 
  dplyr::summarize(
    genus_a = mean(a, na.rm = TRUE),
    genus_b = mean(b, na.rm = TRUE)
  )

### Fill in missing a/b coefficients, add to bio table
ab_table <- ab_info %>%
  dplyr::left_join(genus_ab_info, by = "Genus")

ab_table <- ab_table %>% 
  dplyr::mutate(
    a = ifelse(is.na(a),
      genus_a,
      a
    ),
    b = ifelse(is.na(b),
      genus_b,
      b
    )
  ) %>% 
  dplyr::select(Species, a, b)

ab_table
```

    ## # A tibble: 131,941 x 3
    ##    Species                        a     b
    ##    <chr>                      <dbl> <dbl>
    ##  1 Aapticheilichthys websteri   NaN   NaN
    ##  2 Aaptolasma americana         NaN   NaN
    ##  3 Aaptolasma brintoni          NaN   NaN
    ##  4 Aaptolasma callistoderma     NaN   NaN
    ##  5 Aaptolasma leptoderma        NaN   NaN
    ##  6 Aaptolasma triderma          NaN   NaN
    ##  7 Aaptos aaptos                NaN   NaN
    ##  8 Aaptos bergmanni             NaN   NaN
    ##  9 Aaptos ciliata               NaN   NaN
    ## 10 Aaptos duchassaingi          NaN   NaN
    ## # … with 131,931 more rows

### Combine lmax and a/b tables

``` r
bio_table <- dplyr::left_join(bio_table, ab_table, by = "Species") %>%
  dplyr::select(SpecCode, Species, Genus, Family, lmax, a, b, server)

bio_table
```

    ## # A tibble: 131,941 x 8
    ##    SpecCode Species           Genus       Family       lmax     a     b server  
    ##       <int> <chr>             <chr>       <chr>       <dbl> <dbl> <dbl> <chr>   
    ##  1    64588 Aapticheilichthy… Aapticheil… Procatopo…   3.30   NaN   NaN fishbase
    ##  2    32307 Aaptolasma ameri… Aaptolasma  Bathylasm… NaN      NaN   NaN sealife…
    ##  3    32306 Aaptolasma brint… Aaptolasma  Bathylasm… NaN      NaN   NaN sealife…
    ##  4    32308 Aaptolasma calli… Aaptolasma  Bathylasm… NaN      NaN   NaN sealife…
    ##  5    32304 Aaptolasma lepto… Aaptolasma  Bathylasm… NaN      NaN   NaN sealife…
    ##  6    32305 Aaptolasma tride… Aaptolasma  Bathylasm… NaN      NaN   NaN sealife…
    ##  7    51720 Aaptos aaptos     Aaptos      Suberitid… NaN      NaN   NaN sealife…
    ##  8   165941 Aaptos bergmanni  Aaptos      Suberitid… NaN      NaN   NaN sealife…
    ##  9   105687 Aaptos ciliata    Aaptos      Suberitid… NaN      NaN   NaN sealife…
    ## 10   139407 Aaptos duchassai… Aaptos      Suberitid… NaN      NaN   NaN sealife…
    ## # … with 131,931 more rows

### Trophic levels

``` r
troph_info <- rfishbase::ecology(server = "fishbase") %>% 
  dplyr::select(Species, DietTroph) %>% 
  dplyr::bind_rows(
    rfishbase::ecology(server = "sealifebase") %>% 
      dplyr::select(Species, DietTroph)
  ) %>% 
  dplyr::group_by(Species) %>% 
  dplyr::summarize(species_trophic_level = mean(DietTroph, na.rm=TRUE)) %>% 
  dplyr::left_join(taxa, by = 'Species')
```

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10001 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10002 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10004 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10003 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10001 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10005 ms

    ## Warning: Error in curl::curl_fetch_memory(file, handle): Timeout was reached: [hash-archive.org] Connection timeout after 10001 ms

``` r
### Aggregate at family and genus level
family_troph <- troph_info %>% 
  dplyr::group_by(Family) %>% 
  dplyr::summarize(family_trophic_level = mean(species_trophic_level, na.rm = TRUE))

genus_troph <- troph_info %>% 
  dplyr::group_by(Genus) %>% 
  dplyr::summarize(genus_trophic_level = mean(species_trophic_level, na.rm = TRUE))

trophic_table <- troph_info %>% 
  dplyr::left_join(family_troph, by = "Family") %>% 
  dplyr::left_join(genus_troph, by = "Genus") %>% 
  dplyr::mutate(
    # Try to use the species trophic level.
    # If it's NA, use genus trophic level.
    # If that's NA, use family trophic level, which will then be used regardless if NA
    trophic_level = ifelse(is.na(species_trophic_level),
      ifelse(is.na(genus_trophic_level),
        family_trophic_level,
        genus_trophic_level
      ),
      species_trophic_level
    )
  ) %>% 
  dplyr::select(Species, trophic_level)

trophic_table
```

    ## # A tibble: 131,941 x 2
    ##    Species                    trophic_level
    ##    <chr>                              <dbl>
    ##  1 Aapticheilichthys websteri           NaN
    ##  2 Aaptolasma americana                 NaN
    ##  3 Aaptolasma brintoni                  NaN
    ##  4 Aaptolasma callistoderma             NaN
    ##  5 Aaptolasma leptoderma                NaN
    ##  6 Aaptolasma triderma                  NaN
    ##  7 Aaptos aaptos                        NaN
    ##  8 Aaptos bergmanni                     NaN
    ##  9 Aaptos ciliata                       NaN
    ## 10 Aaptos duchassaingi                  NaN
    ## # … with 131,931 more rows

# Join trophic table with lmax/ab

``` r
bio_table <- dplyr::left_join(bio_table, trophic_table, by = "Species") %>% 
  dplyr::select(SpecCode, Species, Genus, Family, lmax, a, b, trophic_level, server)

bio_table
```

    ## # A tibble: 131,941 x 9
    ##    SpecCode Species     Genus    Family    lmax     a     b trophic_level server
    ##       <int> <chr>       <chr>    <chr>    <dbl> <dbl> <dbl>         <dbl> <chr> 
    ##  1    64588 Aapticheil… Aaptich… Procat…   3.30   NaN   NaN           NaN fishb…
    ##  2    32307 Aaptolasma… Aaptola… Bathyl… NaN      NaN   NaN           NaN seali…
    ##  3    32306 Aaptolasma… Aaptola… Bathyl… NaN      NaN   NaN           NaN seali…
    ##  4    32308 Aaptolasma… Aaptola… Bathyl… NaN      NaN   NaN           NaN seali…
    ##  5    32304 Aaptolasma… Aaptola… Bathyl… NaN      NaN   NaN           NaN seali…
    ##  6    32305 Aaptolasma… Aaptola… Bathyl… NaN      NaN   NaN           NaN seali…
    ##  7    51720 Aaptos aap… Aaptos   Suberi… NaN      NaN   NaN           NaN seali…
    ##  8   165941 Aaptos ber… Aaptos   Suberi… NaN      NaN   NaN           NaN seali…
    ##  9   105687 Aaptos cil… Aaptos   Suberi… NaN      NaN   NaN           NaN seali…
    ## 10   139407 Aaptos duc… Aaptos   Suberi… NaN      NaN   NaN           NaN seali…
    ## # … with 131,931 more rows

### Update data.world file

``` r
library(httr)
library(readr)

readr::write_csv(bio_table, "bio-table.csv")
DW_TOKEN <- Sys.getenv("DW_TOKEN")

# Saving output to res for debugging if necessary
res <- httr::PUT(
  url = "https://api.data.world/v0/uploads/rare/fishbase/files/bio-table.csv",
  config = httr::add_headers(
    Authorization = paste("Bearer", DW_TOKEN),
    "Content-Type" = "application/octet-stream"
  ),
  body = httr::upload_file("bio-table.csv")
)
```
