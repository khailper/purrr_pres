purrr::A_Primer
========================================================
author: Karl Hailperin
date: November 30, 2017
autosize: true
### https://github.com/khailper/purrr_pres

What is purrr?
========================================================

- Package created by Lionel Henry and co-authored by Hadley Wickham, both of RStudio
- Part of the broader tidyverse
- Contains a variety of functions to make programing easier, mostly by reducing for loops

So What?
========================================================
incremental:true

Which would you rather write?

This:

```r
x <- 1:5
sum <- 0
for (i in seq(length(x))){
        sum <- sum + x[i]
}
sum/length(x)
```

```
[1] 3
```

Or this:

```r
x <- 1:5
mean(x)
```

```
[1] 3
```

The map() Family, Part 1: The basics
========================================================
- Basic form: map(.x, .f, ...)
- Creates results like this:

```
results = vector("list", length(.x))
for (i in seq(length(.x))){
        results[i] <- .f(.x[i], ...)
}
results
```


```r
library(purrr)
numbers <- 1:3
map(numbers, sqrt)
```

```
[[1]]
[1] 1

[[2]]
[1] 1.414214

[[3]]
[1] 1.732051
```

The map() Family, Part 2: map_*
========================================================
- Returns a list by default, but the map_* family returns a vector
- For example, map_chr returns a character vector
- Other suffixes include lgl, int, dbl



```r
map_dbl(numbers, sqrt)
```

```
[1] 1.000000 1.414214 1.732051
```

The map() Family, Part 3: Other ways to use .f
========================================================
- If .x is a nested list, using a number for .f returns the nth element of each element of .x
- If the nested lists are named, you can string inputs to get that input


```r
people <- list(c(first_name = "Kristen", last_name = "Bell"), 
               c(first_name = "Ted", last_name = "Danson"), 
               c(first_name = "William", middle_name = "Jackson", last_name = "Harper"))

map_chr(people, 2)
```

```
[1] "Bell"    "Danson"  "Jackson"
```

```r
map_chr(people, "last_name")
```

```
[1] "Bell"   "Danson" "Harper"
```

```r
# map_chr(people, 3) returns error
map_chr(people, 3, .default = NA)
```

```
[1] NA       NA       "Harper"
```

The map() Family, Part 4: anonymous functions
========================================================
- You can add more complex functions in .f with ~ notation


```r
plots <- list(Plot_1 = plot_1, Plot_2 = plot_2, Plot_3 = plot_3)
plot_paths <- map_chr(names(plots), ~ paste0("Plots/", ., ".png"))
```


The map() Family, Part 5: map2()
========================================================
- map2 is like map, but takes two lists, and has the form map2(.x, .y, .f, ...)
- has map2_* functions

```r
vector_1 <- c("Spite", "Research", "Boom", "Far")
vector_2 <- c("Malice", "Development", "Bust", "Away")
map2_chr(vector_1, vector_2, paste, sep = " and ")
```

```
[1] "Spite and Malice"         "Research and Development"
[3] "Boom and Bust"            "Far and Away"            
```

The map() Family, Part 6: pmap()
========================================================
- Instead of creating abitrarily many map[number]() functions, purrr has pwalk(.l, .f, ...)
- .l is a list of lists
- There are pmap_[type] functions

```r
observations <- 1:3
sizes <- 6 # will be recycled
probabilities <- 0.1 * (6:8)
pmap(list(observations,sizes, probabilities), rbinom)
```

```
[[1]]
[1] 3

[[2]]
[1] 4 5

[[3]]
[1] 3 5 6
```

The map() Family, Part 7: invoke_map()
========================================================
- invoke_map(.f, .x, ...) is map in reverse, constant input, list of functions
- There are invoke_map_[type] functions


```r
sample_size <- 5
function_list <- list(rnorm, runif)
invoke_map(function_list, sample_size)
```

```
[[1]]
[1] -0.7606657 -0.5510715 -0.7123391  0.0662748 -0.2277455

[[2]]
[1] 0.42801122 0.17679530 0.52070898 0.03660668 0.68454829
```

The map() Family, Part 8: walk()
========================================================
- walk() is map, but for functions that like write_csv() where we only care about the side effects (the file creation, in this case)


```r
walk2(plot_paths, plots, ggsave)
```

The map() Family, Part 9: modify()
========================================================
- modify(.x , .f, ...) is like map, but it always returns an object of the same type as .x



```r
example_df <- data.frame(x = 1:3, y = 4:6, z = 7:9)
map(example_df, log)
```

```
$x
[1] 0.0000000 0.6931472 1.0986123

$y
[1] 1.386294 1.609438 1.791759

$z
[1] 1.945910 2.079442 2.197225
```

```r
modify(example_df, log)
```

```
          x        y        z
1 0.0000000 1.386294 1.945910
2 0.6931472 1.609438 2.079442
3 1.0986123 1.791759 2.197225
```

The map() Family, Part 10: modify_if/at()
========================================================
- modify_if(.x, .p, .f, ...) applies .f where .p is TRUE, and leavesit alone otherwise
- modify_at(.x, .at, .f, ...) is like modify_if, but .at is character or numeric vector indicating which positions to evaluate .f at














```
Warning message:
package 'knitr' was built under R version 4.1.1 


processing file: purrr_pres.Rpres
Quitting from lines 151-153 (purrr_pres.Rpres) 
Error: Can't coerce element 1 from a double to a integer
Execution halted
```
