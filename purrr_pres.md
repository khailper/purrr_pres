purrr::A_Primer
========================================================
author: Karl Hailperin
date: November 30, 2017
autosize: true

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
x <- c(1,2,3,4,5)
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
x <- c(1,2,3,4,5)
mean(x)
```

```
[1] 3
```

The map() Family, Part 1: The basics
========================================================
incremental:true
 
- Basic form: map(.x,.f, ...)
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
numbers <- c(1,2,3)
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
- Other suffixes are lgl, int, dbl, dfr, dfc



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
people <- list(c("first name" = "Kristen", "last name" = "Bell"), c("first name" = "Ted", "last name" = "Danson"), c("first name" = "William", "middle name" = "Jackson", "last name" = "Harper"))

map_chr(people, 2)
```

```
[1] "Bell"    "Danson"  "Jackson"
```

```r
map_chr(people, "last name")
```

```
[1] "Bell"   "Danson" "Harper"
```

```r
# map_chr(people, 3) # returns error
map_chr(people, 3, .default = NA)
```

```
[1] NA       NA       "Harper"
```

The map() Family, Part 4: anonymous functions
========================================================
- You can add more complex functions in .f with ~ notation


```r
plots <- list("Plot 1" = plot_1, "Plot 2" = plot_2, "Plot 3" = plot_3)
plot_paths <- map_chr(plots, ~ paste0("Plots/", names(.x), ".png"))
```

Note: You can use map() in the anonymous function to create a nested for loop (thanks to [Sharon Machlis](https://twitter.com/sharon000/status/900400803172814849) for pointing this out)

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
[1] 4 4

[[3]]
[1] 4 4 5
```

The map() Family, Part 7: invoke_map()
========================================================
- invoke_map() is map in reverse, constant input, list of functions
- There are pmap_[type] functions

```r
sample_size <- 5
function_list <- list(rnorm, runif)
invoke_map(function_list, sample_size)
```

```
[[1]]
[1] -2.0058734  1.7864206  0.5293020 -0.7553341 -1.2162163

[[2]]
[1] 0.427320862 0.311096732 0.725950402 0.039297120 0.005404793
```

The map() Family, Part 8: walk()
========================================================
- walk() is map, but for functions that like write_csv() where we only care about the side effects (the file creation, in this case)


```r
walk2(plot_paths, plots, ggsave)
```

reduce()
========================================================
- reduce(.x, .f) uses .f to reduce .x to one element, starting with the first 2, than the output and the third,etc.
- If x is a 3-element list/vector, `map(x,f) = f(f(x1,x2),x3)`
- Example: `x = c(1,2,3)`, ``map(x, `+`) = (1 +2) + 3``
- Like map, reduce also has a reduce_2 version
- reduce_right() works like reduce, but from the right
- accumulate(): like reduce(), but returns a vector with all the intermediate values in addition to the final one
    + Example: `x = c(1,2,3)`, ``accumulate(x, `+`)`` returns 1, 3, 6
    + While there's an accumulate_right(), there's no accumulate2()
    

```r
one_to_hundred <- 1:100
reduce(one_to_hundred, `+`)
```

```
[1] 5050
```

safely()
========================================================
- safely(.f) returns a version of .f that handles errors well

```r
safe_sqrt <- safely(sqrt)
safe_sqrt(4)
```

```
$result
[1] 2

$error
NULL
```

```r
safe_sqrt("a")
```

```
$result
NULL

$error
<simpleError in sqrt(x = x): non-numeric argument to mathematical function>
```

- Don't like NULL? Use the otherwise argument.
- .f can use ~ notation.

transpose()
========================================================
- Turns lists inside out
- Note: unless you use the option .names parameter, transpose looks at first item to determine names

```r
transpose(people) %>% simplify_all()
```

```
$`first name`
[1] "Kristen" "Ted"     "William"

$`last name`
[1] "Bell"   "Danson" "Harper"
```

Other Resources Because You Found This Talk Unhelpful
========================================================
- Charlotte Wickham: [https://github.com/cwickham/purrr-tutorial/blob/master/slides.pdf](https://github.com/cwickham/purrr-tutorial/blob/master/slides.pdf)
- Julia Silge:[https://jennybc.github.io/purrr-tutorial/index.html](https://jennybc.github.io/purrr-tutorial/index.html)

