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
plots <- list("Plot 1" = plot_1, "Plot 2" = plot_2, "Plot 3" = plot_3)
plot_paths <- map_chr(plots, ~ paste0("Plots/", names(.), ".png"))
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
[1] 4

[[2]]
[1] 4 4

[[3]]
[1] 6 4 5
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
[1]  0.9311378 -0.5683189 -1.0530441  0.1370340  0.8697368

[[2]]
[1] 0.59892808 0.52088840 0.59509439 0.09486915 0.23568217
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


```r
modify_if(1:7,  ~ . %% 2  == 0, log) %>% simplify()
```

```
[1] 1.0000000 0.6931472 3.0000000 1.3862944 5.0000000 1.7917595 7.0000000
```

```r
modify_at(example_df, "x", log)
```

```
          x y z
1 0.0000000 4 7
2 0.6931472 5 8
3 1.0986123 6 9
```

The map() Family, Part 11a: modify_depth()
========================================================
- modify_depth(.x, .depth, .f, ...) is for mapping over elements buried in a nested list
- .depth = 0 is the list itself
- .depth = 1 is the elements of the list (modify_depth(.x, 1, .f is the same as map(.x, ,f)))
- .depth = 2 is the elements of those lists (assuming .x is a list of  lists)


```r
casts <- list(
        the_good_place = people,
        crazy_ex_girlriend = list(c(first_name = "Rachel", last_name = "Bloom"), 
                                       c(first_name = "Vincent", last_name = "Rodriguez"), 
                                       c(first_name = "Donna", middle_name = "Lynne", last_name = "Champlin"))
)
```

The map() Family, Part 11b: modify_depth()
========================================================


```r
map(casts, toupper)
```

```
$the_good_place
[1] "C(\"KRISTEN\", \"BELL\")"               
[2] "C(\"TED\", \"DANSON\")"                 
[3] "C(\"WILLIAM\", \"JACKSON\", \"HARPER\")"

$crazy_ex_girlriend
[1] "C(\"RACHEL\", \"BLOOM\")"             
[2] "C(\"VINCENT\", \"RODRIGUEZ\")"        
[3] "C(\"DONNA\", \"LYNNE\", \"CHAMPLIN\")"
```

```r
modify_depth(casts, 2, toupper)
```

```
$the_good_place
$the_good_place[[1]]
first_name  last_name 
 "KRISTEN"     "BELL" 

$the_good_place[[2]]
first_name  last_name 
     "TED"   "DANSON" 

$the_good_place[[3]]
 first_name middle_name   last_name 
  "WILLIAM"   "JACKSON"    "HARPER" 


$crazy_ex_girlriend
$crazy_ex_girlriend[[1]]
first_name  last_name 
  "RACHEL"    "BLOOM" 

$crazy_ex_girlriend[[2]]
 first_name   last_name 
  "VINCENT" "RODRIGUEZ" 

$crazy_ex_girlriend[[3]]
 first_name middle_name   last_name 
    "DONNA"     "LYNNE"  "CHAMPLIN" 
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
$first_name
[1] "Kristen" "Ted"     "William"

$last_name
[1] "Bell"   "Danson" "Harper"
```

Other Resources Because You Found This Talk Unhelpful
========================================================
- Charlotte Wickham: [https://github.com/cwickham/purrr-tutorial/blob/master/slides.pdf](https://github.com/cwickham/purrr-tutorial/blob/master/slides.pdf)
- Julia Silge:[https://jennybc.github.io/purrr-tutorial/index.html](https://jennybc.github.io/purrr-tutorial/index.html)

