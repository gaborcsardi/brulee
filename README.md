
<!-- README.md is generated from README.Rmd. Please edit that file -->

# brulee <a href="https://brulee.tidymodels.org/"><img src="man/figures/logo.png" align="right" height="139" alt="a dish of creme brulee on a striped background" /></a>

<!-- badges: start -->

[![R-CMD-check](https://github.com/tidymodels/brulee/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/tidymodels/brulee/actions/workflows/R-CMD-check.yaml)
[![Codecov test
coverage](https://codecov.io/gh/tidymodels/brulee/branch/main/graph/badge.svg)](https://app.codecov.io/gh/tidymodels/brulee?branch=main)
[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html)
<!-- badges: end -->

The R `brulee` package contains several basic modeling functions that
use the `torch` package infrastructure, such as:

- [neural
  networks](https://brulee.tidymodels.org/reference/brulee_mlp.html)
- [linear
  regression](https://brulee.tidymodels.org/reference/brulee_linear_reg.html)
- [logistic
  regression](https://brulee.tidymodels.org/reference/brulee_logistic_reg.html)
- [multinomial
  regression](https://brulee.tidymodels.org/reference/brulee_multinomial_reg.html)

Some interesting features:

- Early stopping based on an internal validation set.
- L1 and L2 penalization, also dropout.
- GPU utilization for models.
- Class-specific weights for cost-sensitive learning.
- Data frame, matrix, formula, and recipe interfaces for models.

## Installation

You can install the released version of brulee from
[CRAN](https://CRAN.R-project.org) with:

``` r
install.packages("brulee")
```

And the development version from
[GitHub](https://github.com/tidymodels/brulee) with:

``` r
# install.packages("pak")
pak::pak("tidymodels/brulee")
```

## Example

`brulee` has formula, x/y, and recipe user interfaces for each function.
For example:

``` r
library(brulee)
library(recipes)
library(yardstick)

data(bivariate, package = "modeldata")
set.seed(20)
nn_log_biv <- brulee_mlp(Class ~ log(A) + log(B), 
                         data = bivariate_train, 
                         hidden_units = 3)

# We use the tidymodels semantics to always return a tibble when predicting
predict(nn_log_biv, bivariate_test, type = "prob") %>% 
  bind_cols(bivariate_test) %>% 
  roc_auc(Class, .pred_One)
#> # A tibble: 1 × 3
#>   .metric .estimator .estimate
#>   <chr>   <chr>          <dbl>
#> 1 roc_auc binary         0.838
```

A recipe can also be used if the data require some sort of preprocessing
(e.g., indicator variables, transformations, or standardization):

``` r
library(recipes)

rec <- 
  recipe(Class ~ ., data = bivariate_train) %>%  
  step_YeoJohnson(all_numeric_predictors()) %>% 
  step_normalize(all_numeric_predictors())

set.seed(20)
nn_rec_biv <- brulee_mlp(rec, data = bivariate_train, 
                         hidden_units = 3)

predict(nn_rec_biv, bivariate_test, type = "prob") %>% 
  bind_cols(bivariate_test) %>% 
  roc_auc(Class, .pred_One)
#> # A tibble: 1 × 3
#>   .metric .estimator .estimate
#>   <chr>   <chr>          <dbl>
#> 1 roc_auc binary         0.866
```

## Code of Conduct

Please note that the brulee project is released with a [Contributor Code
of
Conduct](https://contributor-covenant.org/version/2/0/CODE_OF_CONDUCT.html).
By contributing to this project, you agree to abide by its terms.
