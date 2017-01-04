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
#> 1 pointestimate     1 treatment               1 0.2799562 0.02154427
#> 2 pointestimate     1 treatment               0 0.4411912 0.02091986
#> 3 pointestimate     2 treatment               1 0.4574562 0.02281224
#> 4 pointestimate     2 treatment               0 0.2141744 0.02218771
#> 5 pointestimate     3 treatment               1 0.2629705 0.02156781
#> 6 pointestimate     3 treatment               0 0.3456699 0.02075912
#>   ci.level  ci.lower  ci.upper                       label
#> 1     0.95 0.2376846 0.3219419 Topic 1(Covariate Level: 1)
#> 2     0.95 0.3999327 0.4824369 Topic 1(Covariate Level: 1)
#> 3     0.95 0.4128875 0.5020247 Topic 2(Covariate Level: 1)
#> 4     0.95 0.1733466 0.2584113 Topic 2(Covariate Level: 1)
#> 5     0.95 0.2227190 0.3069685 Topic 3(Covariate Level: 1)
#> 6     0.95 0.3058800 0.3861812 Topic 3(Covariate Level: 1)
```

``` r

## This time, let the treatment effect vary (linearly) by pid_rep.
prep <- estimateEffect(formula = 1:3 ~ treatment + pid_rep + treatment:pid_rep,
                       stmobj = gadarianFit,
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
