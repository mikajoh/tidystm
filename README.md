<!-- README.md is generated from README.Rmd. Please edit that file -->
### tidystm: Extract (tidy) effect from `estimateEffect` in the [stm package](http://www.structuraltopicmodel.com/)

[![Travis-CI Build Status](https://travis-ci.org/.svg?branch=master)](https://travis-ci.org/) [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/mikaelpoul/tidystm?branch=master&svg=true)](https://ci.appveyor.com/project/mikaelpoul/tidystm)

Extracts the effect of a covariate on a set of topics selected by the user. Different effect types available depending on type of covariate. Before running this, the user should run a function to simulate necessary confidence intervals. See `estimateEffect` of the [stm package](http://www.structuraltopicmodel.com/).

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
## Load the packages and set seed
library(stm)
library(tidystm)

set.seed(2016)

## Load the example data from the stm pacakge
data(gadarian)

## Estimate the effect on all three topics and return the point
## estimates in a tidy data frame
prep <- estimateEffect(1:3 ~ treatment, gadarianFit, gadarian)
effect <- extract.estimateEffect(prep, "treatment", model = gadarianFit, method = "pointestimate")

print(effect)
#>          method topic covariate covariate.value  estimate  std.error
#> 1 pointestimate     1 treatment               1 0.2804612 0.02060757
#> 2 pointestimate     1 treatment               0 0.4439191 0.02369964
#> 3 pointestimate     2 treatment               1 0.4569280 0.02298090
#> 4 pointestimate     2 treatment               0 0.2099581 0.02143746
#> 5 pointestimate     3 treatment               1 0.2629073 0.01962893
#> 6 pointestimate     3 treatment               0 0.3459851 0.02258847
#>   ci.level  ci.lower  ci.upper                       label
#> 1     0.95 0.2402753 0.3201613 Topic 1(Covariate Level: 1)
#> 2     0.95 0.3987170 0.4904847 Topic 1(Covariate Level: 1)
#> 3     0.95 0.4121723 0.5011797 Topic 2(Covariate Level: 1)
#> 4     0.95 0.1670091 0.2519835 Topic 2(Covariate Level: 1)
#> 5     0.95 0.2244965 0.3043431 Topic 3(Covariate Level: 1)
#> 6     0.95 0.3024164 0.3903011 Topic 3(Covariate Level: 1)
```

You can then use the results however you like. This is especially helpful if you want to plot it for yourself when the included plot functions doesnt cut it. For example:

``` r

## This time, lets estimate treatment effect as a function of party
## id. We can than get an idea of whether the treatment effect vary
## for people with different ids.
processed <- textProcessor(documents = gadarian$open.ended.response,
                           metadata = gadarian)
#> Building corpus... 
#> Converting to Lower Case... 
#> Removing stopwords... 
#> Removing numbers... 
#> Removing punctuation... 
#> Stemming... 
#> Creating Output...

out <- prepDocuments(document = processed$documents,
                     vocab = processed$vocab,
                     meta = processed$meta)
#> Removing 619 of 1074 terms (619 of 3731 tokens) due to frequency 
#> Your corpus now has 341 documents, 455 terms and 3112 tokens.

stm_fit <- stm(documents = out$documents,
               vocab = out$vocab,
               K = 3,
               prevalence = ~ treatment + pid_rep + treatment:pid_rep,
               data = gadarian)
#> Beginning Initialization.
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 1 (approx. per word bound = -5.514) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 2 (approx. per word bound = -5.451, relative change = 1.146e-02) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 3 (approx. per word bound = -5.427, relative change = 4.454e-03) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 4 (approx. per word bound = -5.416, relative change = 2.047e-03) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 5 (approx. per word bound = -5.410, relative change = 1.125e-03) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, countri, american, citizen 
#>  Topic 3: peopl, come, think, legal, will 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 6 (approx. per word bound = -5.406, relative change = 7.216e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 7 (approx. per word bound = -5.403, relative change = 5.284e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 8 (approx. per word bound = -5.401, relative change = 4.328e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 9 (approx. per word bound = -5.398, relative change = 3.853e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 10 (approx. per word bound = -5.397, relative change = 3.653e-04) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, countri, american, citizen 
#>  Topic 3: peopl, come, think, legal, will 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 11 (approx. per word bound = -5.395, relative change = 3.569e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 12 (approx. per word bound = -5.393, relative change = 3.481e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 13 (approx. per word bound = -5.391, relative change = 3.329e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 14 (approx. per word bound = -5.389, relative change = 3.150e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 15 (approx. per word bound = -5.388, relative change = 3.013e-04) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, countri, american, citizen 
#>  Topic 3: peopl, come, think, legal, will 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 16 (approx. per word bound = -5.386, relative change = 2.969e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 17 (approx. per word bound = -5.384, relative change = 3.032e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 18 (approx. per word bound = -5.383, relative change = 3.196e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 19 (approx. per word bound = -5.381, relative change = 3.432e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 20 (approx. per word bound = -5.379, relative change = 3.707e-04) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, countri, american, citizen 
#>  Topic 3: peopl, come, think, legal, will 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 21 (approx. per word bound = -5.377, relative change = 3.981e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 22 (approx. per word bound = -5.374, relative change = 4.250e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 23 (approx. per word bound = -5.372, relative change = 4.524e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 24 (approx. per word bound = -5.369, relative change = 4.798e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 25 (approx. per word bound = -5.367, relative change = 5.037e-04) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, countri, american, citizen 
#>  Topic 3: peopl, come, think, legal, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 26 (approx. per word bound = -5.364, relative change = 5.176e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 27 (approx. per word bound = -5.361, relative change = 5.183e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 28 (approx. per word bound = -5.358, relative change = 5.090e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 29 (approx. per word bound = -5.356, relative change = 4.979e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 30 (approx. per word bound = -5.353, relative change = 4.940e-04) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, countri, american, citizen 
#>  Topic 3: peopl, come, think, legal, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 31 (approx. per word bound = -5.350, relative change = 5.037e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 32 (approx. per word bound = -5.348, relative change = 5.282e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 33 (approx. per word bound = -5.345, relative change = 5.630e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 34 (approx. per word bound = -5.341, relative change = 5.974e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 35 (approx. per word bound = -5.338, relative change = 6.212e-04) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, countri, american, citizen 
#>  Topic 3: peopl, come, think, legal, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 36 (approx. per word bound = -5.335, relative change = 6.308e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 37 (approx. per word bound = -5.331, relative change = 6.296e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 38 (approx. per word bound = -5.328, relative change = 6.214e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 39 (approx. per word bound = -5.325, relative change = 6.061e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 40 (approx. per word bound = -5.322, relative change = 5.825e-04) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, american, countri, need 
#>  Topic 3: peopl, come, think, legal, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 41 (approx. per word bound = -5.319, relative change = 5.517e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 42 (approx. per word bound = -5.316, relative change = 5.150e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 43 (approx. per word bound = -5.313, relative change = 4.681e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 44 (approx. per word bound = -5.311, relative change = 4.053e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 45 (approx. per word bound = -5.309, relative change = 3.499e-04) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, american, need, will 
#>  Topic 3: peopl, come, think, legal, countri 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 46 (approx. per word bound = -5.308, relative change = 3.201e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 47 (approx. per word bound = -5.306, relative change = 3.040e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 48 (approx. per word bound = -5.305, relative change = 2.872e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 49 (approx. per word bound = -5.303, relative change = 2.717e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 50 (approx. per word bound = -5.302, relative change = 2.585e-04) 
#> Topic 1: illeg, get, tax, border, pay 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, legal 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 51 (approx. per word bound = -5.301, relative change = 2.423e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 52 (approx. per word bound = -5.299, relative change = 2.205e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 53 (approx. per word bound = -5.298, relative change = 1.972e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 54 (approx. per word bound = -5.297, relative change = 1.782e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 55 (approx. per word bound = -5.297, relative change = 1.660e-04) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, legal 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 56 (approx. per word bound = -5.296, relative change = 1.590e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 57 (approx. per word bound = -5.295, relative change = 1.534e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 58 (approx. per word bound = -5.294, relative change = 1.466e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 59 (approx. per word bound = -5.293, relative change = 1.385e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 60 (approx. per word bound = -5.293, relative change = 1.317e-04) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, legal 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 61 (approx. per word bound = -5.292, relative change = 1.279e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 62 (approx. per word bound = -5.291, relative change = 1.273e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 63 (approx. per word bound = -5.291, relative change = 1.293e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 64 (approx. per word bound = -5.290, relative change = 1.327e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 65 (approx. per word bound = -5.289, relative change = 1.363e-04) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, legal 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 66 (approx. per word bound = -5.288, relative change = 1.381e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 67 (approx. per word bound = -5.288, relative change = 1.360e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 68 (approx. per word bound = -5.287, relative change = 1.303e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 69 (approx. per word bound = -5.286, relative change = 1.229e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 70 (approx. per word bound = -5.286, relative change = 1.163e-04) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, legal 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 71 (approx. per word bound = -5.285, relative change = 1.112e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 72 (approx. per word bound = -5.285, relative change = 1.070e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 73 (approx. per word bound = -5.284, relative change = 1.032e-04) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 74 (approx. per word bound = -5.284, relative change = 9.887e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 75 (approx. per word bound = -5.283, relative change = 9.463e-05) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 76 (approx. per word bound = -5.283, relative change = 9.031e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 77 (approx. per word bound = -5.282, relative change = 8.683e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 78 (approx. per word bound = -5.282, relative change = 8.477e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 79 (approx. per word bound = -5.281, relative change = 8.505e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 80 (approx. per word bound = -5.281, relative change = 8.561e-05) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 81 (approx. per word bound = -5.280, relative change = 8.299e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 82 (approx. per word bound = -5.280, relative change = 7.646e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 83 (approx. per word bound = -5.280, relative change = 6.790e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 84 (approx. per word bound = -5.279, relative change = 6.090e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 85 (approx. per word bound = -5.279, relative change = 5.512e-05) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 86 (approx. per word bound = -5.279, relative change = 5.113e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 87 (approx. per word bound = -5.278, relative change = 4.839e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 88 (approx. per word bound = -5.278, relative change = 4.598e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 89 (approx. per word bound = -5.278, relative change = 4.393e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 90 (approx. per word bound = -5.278, relative change = 4.163e-05) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 91 (approx. per word bound = -5.278, relative change = 3.942e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 92 (approx. per word bound = -5.277, relative change = 3.745e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 93 (approx. per word bound = -5.277, relative change = 3.632e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 94 (approx. per word bound = -5.277, relative change = 3.603e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 95 (approx. per word bound = -5.277, relative change = 3.646e-05) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 96 (approx. per word bound = -5.277, relative change = 3.779e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 97 (approx. per word bound = -5.276, relative change = 3.962e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 98 (approx. per word bound = -5.276, relative change = 4.133e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 99 (approx. per word bound = -5.276, relative change = 4.230e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 100 (approx. per word bound = -5.276, relative change = 4.146e-05) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 101 (approx. per word bound = -5.275, relative change = 3.880e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 102 (approx. per word bound = -5.275, relative change = 3.488e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 103 (approx. per word bound = -5.275, relative change = 3.143e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 104 (approx. per word bound = -5.275, relative change = 2.842e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 105 (approx. per word bound = -5.275, relative change = 2.648e-05) 
#> Topic 1: illeg, get, tax, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 106 (approx. per word bound = -5.275, relative change = 2.528e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 107 (approx. per word bound = -5.275, relative change = 2.463e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 108 (approx. per word bound = -5.274, relative change = 2.390e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 109 (approx. per word bound = -5.274, relative change = 2.375e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 110 (approx. per word bound = -5.274, relative change = 2.431e-05) 
#> Topic 1: illeg, tax, get, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 111 (approx. per word bound = -5.274, relative change = 2.559e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 112 (approx. per word bound = -5.274, relative change = 2.779e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 113 (approx. per word bound = -5.274, relative change = 2.937e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 114 (approx. per word bound = -5.274, relative change = 2.864e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 115 (approx. per word bound = -5.273, relative change = 2.581e-05) 
#> Topic 1: illeg, tax, get, pay, take 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 116 (approx. per word bound = -5.273, relative change = 2.367e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 117 (approx. per word bound = -5.273, relative change = 2.232e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 118 (approx. per word bound = -5.273, relative change = 2.191e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 119 (approx. per word bound = -5.273, relative change = 2.219e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 120 (approx. per word bound = -5.273, relative change = 2.240e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 121 (approx. per word bound = -5.273, relative change = 2.306e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 122 (approx. per word bound = -5.273, relative change = 2.330e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 123 (approx. per word bound = -5.273, relative change = 2.394e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 124 (approx. per word bound = -5.272, relative change = 2.419e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 125 (approx. per word bound = -5.272, relative change = 2.480e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, need 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 126 (approx. per word bound = -5.272, relative change = 2.521e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 127 (approx. per word bound = -5.272, relative change = 2.611e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 128 (approx. per word bound = -5.272, relative change = 2.695e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 129 (approx. per word bound = -5.272, relative change = 2.809e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 130 (approx. per word bound = -5.272, relative change = 2.897e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, worri 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 131 (approx. per word bound = -5.271, relative change = 3.026e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 132 (approx. per word bound = -5.271, relative change = 3.106e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 133 (approx. per word bound = -5.271, relative change = 3.155e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 134 (approx. per word bound = -5.271, relative change = 3.162e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 135 (approx. per word bound = -5.271, relative change = 3.136e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, worri 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 136 (approx. per word bound = -5.271, relative change = 3.036e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 137 (approx. per word bound = -5.270, relative change = 2.897e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 138 (approx. per word bound = -5.270, relative change = 2.691e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 139 (approx. per word bound = -5.270, relative change = 2.486e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 140 (approx. per word bound = -5.270, relative change = 2.216e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, worri 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 141 (approx. per word bound = -5.270, relative change = 1.969e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 142 (approx. per word bound = -5.270, relative change = 1.749e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 143 (approx. per word bound = -5.270, relative change = 1.568e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 144 (approx. per word bound = -5.270, relative change = 1.453e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 145 (approx. per word bound = -5.270, relative change = 1.390e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, worri 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 146 (approx. per word bound = -5.270, relative change = 1.406e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 147 (approx. per word bound = -5.269, relative change = 1.477e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 148 (approx. per word bound = -5.269, relative change = 1.608e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 149 (approx. per word bound = -5.269, relative change = 1.805e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 150 (approx. per word bound = -5.269, relative change = 2.043e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, worri 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 151 (approx. per word bound = -5.269, relative change = 2.285e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 152 (approx. per word bound = -5.269, relative change = 2.498e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 153 (approx. per word bound = -5.269, relative change = 2.741e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 154 (approx. per word bound = -5.269, relative change = 2.965e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 155 (approx. per word bound = -5.268, relative change = 3.208e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, worri 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 156 (approx. per word bound = -5.268, relative change = 3.459e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 157 (approx. per word bound = -5.268, relative change = 3.734e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 158 (approx. per word bound = -5.268, relative change = 4.048e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 159 (approx. per word bound = -5.268, relative change = 4.396e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 160 (approx. per word bound = -5.267, relative change = 4.917e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, worri 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 161 (approx. per word bound = -5.267, relative change = 5.754e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 162 (approx. per word bound = -5.267, relative change = 6.086e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 163 (approx. per word bound = -5.266, relative change = 5.207e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 164 (approx. per word bound = -5.266, relative change = 4.069e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 165 (approx. per word bound = -5.266, relative change = 3.576e-05) 
#> Topic 1: illeg, tax, pay, take, get 
#>  Topic 2: immigr, job, american, will, worri 
#>  Topic 3: peopl, countri, come, think, work 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 166 (approx. per word bound = -5.266, relative change = 3.199e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 167 (approx. per word bound = -5.266, relative change = 2.466e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 168 (approx. per word bound = -5.266, relative change = 1.633e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Completing Iteration 169 (approx. per word bound = -5.266, relative change = 1.142e-05) 
#> .................................................................................................................
#> Completed E-Step (0 seconds). 
#> Completed M-Step. 
#> Model Converged

prep <- estimateEffect(formula = 1:3 ~ treatment + pid_rep + treatment:pid_rep,
                       stmobj = stm_fit,
                       metadata = gadarian)


## And lets plot it using the included plotting function.
op <- par(mfrow = c(1, 2))
for (i in c(0, 1)) {
  plot.estimateEffect(x = prep,
                      covariate = "pid_rep",
                      method = "continuous",
                      model = gadarianFit,
                      labeltype = "frex",
                      moderator = "treatment",
                      moderator.value = i)
}
```

![](README-unnamed-chunk-5-1.png)

``` r
par(op)
```

Not very easy to see what going on. Instead, lets extract the estimates in a tidy format so that we can plot it ourselves

``` r
## Lets extract the estimates in a tidy format so that we can plot it
## ourselves. We can now use lapply instead to first run it with
## moderator.value 0 and then with moderator.value 1, and then bind
## the two data frames together.
effect <- lapply(c(0, 1), function(i) {
  extract.estimateEffect(x = prep,
                         covariate = "pid_rep",
                         method = "continuous",
                         model = gadarianFit,
                         labeltype = "frex",
                         n = 4,
                         moderator = "treatment",
                         moderator.value = i)
})
effect <- do.call("rbind", effect)

## And, for example, plot it with ggplot2 and facet by topic instead.
library(ggplot2)

ggplot(effect, aes(x = covariate.value, y = estimate,
                   ymin = ci.lower, ymax = ci.upper,
                   group = moderator.value,
                   fill = factor(moderator.value))) +
  facet_wrap(~ label, nrow = 2) +
  geom_ribbon(alpha = .5) +
  geom_line() +
  scale_x_continuous(labels = function(x) ifelse(x == 1, "1\nREP", ifelse(x == 0, "0\nDEM", x))) +
  labs(x = "Party ID",
       y = "Expected Topic Proportion",
       fill = "Treated (0/1)") +
  theme(legend.position = "bottom")
```

![](README-unnamed-chunk-6-1.png)

Much better :)
