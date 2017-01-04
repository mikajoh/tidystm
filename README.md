<!-- README.md is generated from README.Rmd. Please edit that file -->
### tidystm: Extract (tidy) effect from `estimateEffect` in the `stm` package

[![Travis-CI Build Status](https://travis-ci.org/.svg?branch=master)](https://travis-ci.org/)

Extracts the effect of a covariate on a set of topics selected by the user. Different effect types available depending on type of covariate. Before running this, the user should run a function to simulate necessary confidence intervals. See estimateEffect of the stm package.

#### Install

You need the `devtools` package in order to install `tidystm`. You can install it using the follow code (note that you only need to run this once):

``` r
if (!require(devtools)) install.packages("devtools")
```

You can then install `tidystm` by running:

``` r
devtools::install_github("mikaelpoul/tidystm", dependencies = TRUE)
```

#### Use

The package, for now, includes one function: `extract.estimateEffect()`. You run it just as you would run the `plot.estimateEffect()` function included in the `stm` package. E.g.:

``` r
## Load the packages
library(stm)
library(tidystm)

## Load the example data from the stm pacakge
data(gadarian)

## Estimate the effect on all three topics and return the point
## estimates in a tidy data frame
prep <- estimateEffect(1:3 ~ treatment, gadarianFit, gadarian)
effect <- extract.estimateEffect(prep, "treatment", model = gadarianFit, method = "pointestimate")

print(effect)
#>          method topic covariate covariate.value  estimate  std.error
#> 1 pointestimate     1 treatment               1 0.2822001 0.02079412
#> 2 pointestimate     1 treatment               0 0.4427798 0.02326116
#> 3 pointestimate     2 treatment               1 0.4570794 0.02132364
#> 4 pointestimate     2 treatment               0 0.2081384 0.02233287
#> 5 pointestimate     3 treatment               1 0.2613402 0.01901003
#> 6 pointestimate     3 treatment               0 0.3486364 0.02074318
#>   ci.level  ci.lower  ci.upper                       label
#> 1     0.95 0.2418038 0.3256187 Topic 1(Covariate Level: 1)
#> 2     0.95 0.3983617 0.4914971 Topic 2(Covariate Level: 1)
#> 3     0.95 0.4152041 0.4979726 Topic 3(Covariate Level: 1)
#> 4     0.95 0.1651731 0.2511814 Topic 1(Covariate Level: 0)
#> 5     0.95 0.2225678 0.2980599 Topic 2(Covariate Level: 0)
#> 6     0.95 0.3074906 0.3870535 Topic 3(Covariate Level: 0)
```
