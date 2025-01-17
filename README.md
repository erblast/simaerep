
<!-- README.md is generated from README.Rmd. Please edit that file -->

# simaerep <a href='https://openpharma.github.io/simaerep'><img src='man/figures/logo.png' align="right" height="139" /></a>

<!-- badges: start -->

[![R build
status](https://github.com/openpharma/simaerep/workflows/R-CMD-check/badge.svg)](https://github.com/openpharma/simaerep/actions)
[![codecov](https://codecov.io/gh/openpharma/simaerep/branch/master/graph/badge.svg)](https://codecov.io/gh/openpharma/simaerep)
[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
<!-- badges: end -->

Simulate adverse event reporting in clinical trials with the goal of
detecting under-reporting sites.

Monitoring of Adverse Event (AE) reporting in clinical trials is
important for patient safety. We use bootstrap-based simulation to
assign an AE under-reporting probability to each site in a clinical
trial. The method is inspired by the ‘infer’ R package and Allen
Downey’s blog article: [“There is only one
test!”](http://allendowney.blogspot.com/2011/05/there-is-only-one-test.html).

## Installation

You can install the development version from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("openpharma/simaerep")
```

## Publication

Koneswarakantha, B., Barmaz, Y., Ménard, T. et al. Follow-up on the Use
of Advanced Analytics for Clinical Quality Assurance: Bootstrap
Resampling to Enhance Detection of Adverse Event Under-Reporting. Drug
Saf (2020). <https://doi.org/10.1007/s40264-020-01011-5>

# Vignettes/ Articles

-   [Introduction](https://openpharma.github.io/simaerep/articles/intro.html)
-   [Usability
    Limits](https://openpharma.github.io/simaerep/articles/usability_limits.html)
-   [Check Poisson Test
    Applicability](https://openpharma.github.io/simaerep/articles/check_poisson.html)
-   [SAS
    Files](https://openpharma.github.io/simaerep/articles/sas_files.html)
-   [Adjusted Evaluation Point
    visit_med75](https://openpharma.github.io/simaerep/articles/visit_med75.html)
-   [Aggregate AEs by Days or
    Visit?](https://openpharma.github.io/simaerep/articles/visits_or_days.html)

# Application

``` r
suppressPackageStartupMessages(library(simaerep))
suppressPackageStartupMessages(library(tidyverse))
suppressPackageStartupMessages(library(knitr))

set.seed(1)

df_visit <- sim_test_data_study(
  n_pat = 1000, # number of patients in study
  n_sites = 100, # number of sites in study
  frac_site_with_ur = 0.05, # fraction of sites under-reporting
  ur_rate = 0.4, # rate of under-reporting
  ae_per_visit_mean = 0.5 # mean AE per patient visit
)

df_visit$study_id <- "A"

df_visit %>%
  select(study_id, site_number, patnum, visit, n_ae) %>%
  head(25) %>%
  knitr::kable()
```

| study_id | site_number | patnum  | visit | n_ae |
|:---------|:------------|:--------|------:|-----:|
| A        | S0001       | P000001 |     1 |    0 |
| A        | S0001       | P000001 |     2 |    1 |
| A        | S0001       | P000001 |     3 |    1 |
| A        | S0001       | P000001 |     4 |    2 |
| A        | S0001       | P000001 |     5 |    3 |
| A        | S0001       | P000001 |     6 |    3 |
| A        | S0001       | P000001 |     7 |    3 |
| A        | S0001       | P000001 |     8 |    3 |
| A        | S0001       | P000001 |     9 |    3 |
| A        | S0001       | P000001 |    10 |    3 |
| A        | S0001       | P000001 |    11 |    3 |
| A        | S0001       | P000001 |    12 |    3 |
| A        | S0001       | P000001 |    13 |    4 |
| A        | S0001       | P000001 |    14 |    4 |
| A        | S0001       | P000001 |    15 |    4 |
| A        | S0001       | P000001 |    16 |    6 |
| A        | S0001       | P000001 |    17 |    6 |
| A        | S0001       | P000002 |     1 |    0 |
| A        | S0001       | P000002 |     2 |    0 |
| A        | S0001       | P000002 |     3 |    0 |
| A        | S0001       | P000002 |     4 |    0 |
| A        | S0001       | P000002 |     5 |    0 |
| A        | S0001       | P000002 |     6 |    0 |
| A        | S0001       | P000002 |     7 |    0 |
| A        | S0001       | P000002 |     8 |    1 |

``` r
aerep <- simaerep(df_visit)

plot(aerep, study = "A") 
```

<img src="man/figures/README-unnamed-chunk-2-1.png" width="100%" />

*Left panel shows mean AE reporting per site (lightblue and darkblue
lines) against mean AE reporting of the entire study (golden line).
Single sites are plotted in descending order by AE under-reporting
probability on the right panel in which grey lines denote cumulative AE
count of single patients. Grey dots in the left panel plot indicate
sites that were picked for single plotting. AE under-reporting
probability of dark blue lines crossed threshold of 95%. Numbers in the
upper left corner indicate the ratio of patients that have been used for
the analysis against the total number of patients. Patients that have
not been on the study long enough to reach the evaluation point
(visit_med75, see introduction) will be ignored.*
