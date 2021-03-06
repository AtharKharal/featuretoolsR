# featuretoolsR
An R interface to the Python module Featuretools.

# General
`featuretoolsR` provides functionality from the Python module `featuretools`, which aims to automate feature engineering. This package is very much a work in progress as Featuretools offers a lot of functionality. Any PRs are much appreciated.

# Installing
The recommended way is to install this package with devtools: `devtools::install_github("magnusfurugard/featuretoolsR")`.

In addition, you need to have a working Python environment as well as `featuretools` installed. There is a convenience-function available `featuretoolsR::install_featuretools()` for this. Depending on your machines settings it might require you to run it with proper permissions in a terminal. If so, just install it with pip: `pip install featuretools`

# Usage
All functions in `featuretoolsR` comes with documentation, but it's advised to briefly browse through the [Featuretools Python documentation](https://docs.featuretools.com/index.html). It'll cover things like `entities`, `relationships` and `dfs`. 

## Creating an entityset
An entityset is the set which contain all your entities. To create a set and add an entity straight away, you can use `as_entityset`. 
```
# Libs
library(featuretoolsR)
library(magrittr)

# Create some mock data
set_1 <- data.frame(key = 1:100, value = sample(letters, 100, T))
set_2 <- data.frame(key = 1:100, value = sample(LETTERS, 100, T))

# Create entityset
es <- as_entityset(set_1, index = "key", entity_id = "set_1", id = "demo")
```

## Adding entities
To add entities (i.e if you have relational data across multiple `data.frames`), this can be achieved with `add_entity`. This function is pipe friendly. For this demo-case, we'll use `set_2`.
```
es <- es %>%
  add_entity(
    df = set_2, 
    entity_id = "set_2", 
    index = "key"
  )
```

## Defining relationships
With relational data, it's useful to define a relationship between two or more entities. This can be done with `add_relationship`.
```
es <- es %>%
  add_relationship(
    set1 = "set_1", 
    set2 = "set_2", 
    idx = "key"
  )
```

## Deep feature synthesis
The bread and butter of Featuretools is the `dfs`-function (official docs [here](https://docs.featuretools.com/automated_feature_engineering/afe.html#)). It will attempt to create features based on `*_primitives` you provide (more on primitives below).
```
ft_matrix <- es %>%
  dfs(
    target_entity = "set_1", 
    trans_primitives = c("and", "divide")
  )
```

## Tidying up
To use the new data.frame/features created by `dfs`, a function unique for `featuretoolsR`, `tidy_feature_matrix` can be used. A few "nice-to-have" arguments can be passed to clean the new data, like removing near zero variance variables, as well as replacing `NaN` with `NA`.
```
tidy <- tidy_feature_matrix(ft_matrix, remove_nzv = T, nan_is_na = T)
```

# Primitives
Featuretools supports a lot of primitives. These are accessible with the function `list_primitives()` which returns a data.frame containing type (aggregation (`agg_primitives`) or transform (`trans_primitives`)), name (in the example above, "and" and "divide") as well as a brief description of the primitive itself.

# Credits
[reticulate](https://github.com/rstudio/reticulate) - an R interface to Python.

[Featuretools](https://github.com/Featuretools/featuretools)
