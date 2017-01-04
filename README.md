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
#> 1 pointestimate     1 treatment               1 0.2778767 0.02292685
#> 2 pointestimate     1 treatment               0 0.4411198 0.02215475
#> 3 pointestimate     2 treatment               1 0.4619813 0.02438323
#> 4 pointestimate     2 treatment               0 0.2101236 0.02243251
#> 5 pointestimate     3 treatment               1 0.2596650 0.01931604
#> 6 pointestimate     3 treatment               0 0.3496585 0.02176095
#>   ci.level  ci.lower  ci.upper                       label
#> 1     0.95 0.2344831 0.3227297 Topic 1(Covariate Level: 1)
#> 2     0.95 0.3977314 0.4828091 Topic 1(Covariate Level: 1)
#> 3     0.95 0.4137318 0.5080871 Topic 2(Covariate Level: 1)
#> 4     0.95 0.1653362 0.2532897 Topic 2(Covariate Level: 1)
#> 5     0.95 0.2222134 0.2975350 Topic 3(Covariate Level: 1)
#> 6     0.95 0.3045596 0.3908082 Topic 3(Covariate Level: 1)
```

You can then use the results however you like. This is especially helpful if you want to plot it for yourself when the included plot functions doesnt cut it. For example:

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

Much better :)
