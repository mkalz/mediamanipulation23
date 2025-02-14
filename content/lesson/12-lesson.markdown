---
title: "Space"
linktitle: "12: Space"
date: "2020-05-27"
menu:
  lesson:
    parent: Lessons
    weight: 12
type: docs
toc: true
editor_options: 
  chunk_output_type: console
shiny: true
---




There *is* a short lesson today! You'll learn the basics of joining two different datasets together, both vertically and horizontally.

There are a few imaginary datasets I've created for you to play with:




```r
x
```

```
## # A tibble: 3 x 2
##      id some_variable
##   <dbl> <chr>        
## 1     1 x1           
## 2     2 x2           
## 3     3 x3
```

```r
y
```

```
## # A tibble: 3 x 2
##      id some_other_variable
##   <dbl> <chr>              
## 1     1 y1                 
## 2     2 y2                 
## 3     4 y4
```

```r
national_data
```

```
## # A tibble: 9 x 5
##   state  year unemployment inflation population
##   <chr> <dbl>        <dbl>     <dbl>      <dbl>
## 1 GA     2018          5         2          100
## 2 GA     2019          5.3       1.8        200
## 3 GA     2020          5.2       2.5        300
## 4 NC     2018          6.1       1.8        350
## 5 NC     2019          5.9       1.6        375
## 6 NC     2020          5.3       1.8        400
## 7 CO     2018          4.7       2.7        200
## 8 CO     2019          4.4       2.6        300
## 9 CO     2020          5.1       2.5        400
```

```r
national_data_2019
```

```
## # A tibble: 3 x 4
##   state unemployment inflation population
##   <chr>        <dbl>     <dbl>      <dbl>
## 1 GA             5.3       1.8        200
## 2 NC             5.9       1.6        375
## 3 CO             4.4       2.6        300
```

```r
national_libraries
```

```
## # A tibble: 6 x 4
##   state  year libraries schools
##   <chr> <dbl>     <dbl>   <dbl>
## 1 CO     2018       230     470
## 2 CO     2019       240     440
## 3 CO     2020       270     510
## 4 NC     2018       200     610
## 5 NC     2019       210     590
## 6 NC     2020       220     530
```

```r
national_libraries_2019
```

```
## # A tibble: 2 x 3
##   state libraries schools
##   <chr>     <dbl>   <dbl>
## 1 CO          240     440
## 2 NC          210     590
```

```r
puerto_rico_data
```

```
## # A tibble: 3 x 4
##   state unemployment population  year
##   <chr>        <dbl>      <dbl> <dbl>
## 1 PR             3.1        150  2018
## 2 PR             3.2        250  2019
## 3 PR             3.3        350  2020
```

```r
state_regions
```

```
## # A tibble: 51 x 2
##    region    state
##    <chr>     <chr>
##  1 West      AK   
##  2 South     AL   
##  3 South     AR   
##  4 West      AZ   
##  5 West      CA   
##  6 West      CO   
##  7 Northeast CT   
##  8 South     DC   
##  9 South     DE   
## 10 South     FL   
## # … with 41 more rows
```


## Combining datasets vertically

Recall from the [Lord of the Rings data in exercise 3](/assignment/03-exercise/) that you had to combine three different CSV files into dataset. You used `bind_rows()` to stack each of these on top of each other. 


```r
lotr <- bind_rows(fellowship, tt, rotk)
```

That worked well because each of the individual data frames had the same columns in them, and R was able to line up the matching columns. If columns were missing, R would have placed `NA` in the appropriate locations.

<div class="puzzle">

**Your turn**: Combine `national_data` and `puerto_rico_data` into a single dataset named `us_data` using `bind_rows`. Pay attention to what happens with the inflation column. Also notice that the columns in the Puerto Rico data are in a different order.

</div>

{{% learnr url="https://andrewheiss.shinyapps.io/datavizm20_12-joining-1/" id="learnr-12-lesson-joining1" %}}

## Combining datasets horizontally

Binding rows vertically is the easiest way to combine two datasets, but most often you won't be doing that. You'll only do this if you're combining datasets that come from the same source, like if a state offers separate CSV files of the same data for each county. 

In most cases, though, you'll need to combine completely different datasets, bringing one or more columns from one into another. With vertical combining, R needs column names with the same names in order to figure out where the data lines up. With horizontal combining, R needs values inside one or more columns to be the same in order to figure out where the data lines up.

There is technically a function named `bind_cols()`, but you'll rarely want to use it. It doesn't attempt to match any rows—it just glues two datasets together:


```r
bind_cols(national_data, 
          # Repeat PR 3 times so that it has the same number of rows as national_data
          bind_rows(puerto_rico_data, puerto_rico_data, puerto_rico_data))
```

```
## New names:
## * state -> state...1
## * year -> year...2
## * unemployment -> unemployment...3
## * population -> population...5
## * state -> state...6
## * ...
```

```
## # A tibble: 9 x 9
##   state...1 year...2 unemployment...3 inflation population...5 state...6
##   <chr>        <dbl>            <dbl>     <dbl>          <dbl> <chr>    
## 1 GA            2018              5         2              100 PR       
## 2 GA            2019              5.3       1.8            200 PR       
## 3 GA            2020              5.2       2.5            300 PR       
## 4 NC            2018              6.1       1.8            350 PR       
## 5 NC            2019              5.9       1.6            375 PR       
## 6 NC            2020              5.3       1.8            400 PR       
## 7 CO            2018              4.7       2.7            200 PR       
## 8 CO            2019              4.4       2.6            300 PR       
## 9 CO            2020              5.1       2.5            400 PR       
## # … with 3 more variables: unemployment...7 <dbl>, population...8 <dbl>,
## #   year...9 <dbl>
```

That's… not great.

Instead, we need to use a function that is more careful about bringing in data. Fortunately there are a few good options:

- `inner_join()`
- `left_join()`
- `right_join()`

The **most** helpful way of understanding these different functions [is to go here and stare at the animations for a little while](https://github.com/gadenbuie/tidyexplain#mutating-joins) to see which pieces of which dataset go where. (There are lots of others, like `full_join()`, `semi_join()`, and `anti_join()`, and they have helpful animations, but I rarely use those.)

For each of these functions, **you need at least one common ID column in both datasets** in order for R to know where things line up.

Let's practice how these all work and see what the differences between them are.

## `inner_join()`

First, <a href="https://github.com/gadenbuie/tidyexplain#inner-join" target="_blank">go to this page in a new tab</a> and stare at the mesmerizing animation.

Let's look at two datasets, `x` and `y`:


```r
x
```

```
## # A tibble: 3 x 2
##      id some_variable
##   <dbl> <chr>        
## 1     1 x1           
## 2     2 x2           
## 3     3 x3
```

```r
y
```

```
## # A tibble: 3 x 2
##      id some_other_variable
##   <dbl> <chr>              
## 1     1 y1                 
## 2     2 y2                 
## 3     4 y4
```

Both datasets have an `id` column that is the same across each (though the values aren't necessarily the same). Because there's a shared column, we can join these two based on that column.

If we use `inner_join()`, the resulting dataset will only keep the rows from the first where there are matching values from the second:


```r
inner_join(x, y, by = "id")
```

```
## # A tibble: 2 x 3
##      id some_variable some_other_variable
##   <dbl> <chr>         <chr>              
## 1     1 x1            y1                 
## 2     2 x2            y2
```

Notice how it got rid of the row with `id = 3` from the first and the row with `id = 4` from the second. 

You can also write this with pipes, which is really common when working with **dplyr**:


```r
x %>% 
  inner_join(y, by = "id")
```

```
## # A tibble: 2 x 3
##      id some_variable some_other_variable
##   <dbl> <chr>         <chr>              
## 1     1 x1            y1                 
## 2     2 x2            y2
```

Let's say we have two datasets: `national_data_2019` and `national_libraries_2019`:


```r
national_data_2019
```

```
## # A tibble: 3 x 4
##   state unemployment inflation population
##   <chr>        <dbl>     <dbl>      <dbl>
## 1 GA             5.3       1.8        200
## 2 NC             5.9       1.6        375
## 3 CO             4.4       2.6        300
```

```r
national_libraries_2019
```

```
## # A tibble: 2 x 3
##   state libraries schools
##   <chr>     <dbl>   <dbl>
## 1 CO          240     440
## 2 NC          210     590
```

We want to bring the libraries and schools columns into the general national data. Notice how both datasets have a state column.

<div class="puzzle">

**Your turn**: Create a new dataset named `combined_data` that uses `inner_join()` to merge `national_data_2019` and `national_libraries_2019`.

</div>

{{% learnr url="https://andrewheiss.shinyapps.io/datavizm20_12-joining-2/" id="learnr-12-lesson-joining2" %}}


## `left_join()`

Again, <a href="https://github.com/gadenbuie/tidyexplain#left-join" target="_blank">go to this page in a new tab</a> and stare at the animation.

Left joining is less destructive than inner joining. With left joining, any rows in the first dataset that don't have matches in the second *don't* get thrown away and instead are filled with `NA`:


```r
left_join(x, y, by = "id")
```

```
## # A tibble: 3 x 3
##      id some_variable some_other_variable
##   <dbl> <chr>         <chr>              
## 1     1 x1            y1                 
## 2     2 x2            y2                 
## 3     3 x3            <NA>
```

Notice how the row with `id = 4` from the second dataset is gone, but the row with `id = 3` from the first is still there, with `NA` for `some_other_variable`.

I find this much more useful when combining data. I often have a larger dataset with all the main variables I care about, perhaps with every combination of country and year over 20 years and 180 countries. If I find another dataset I want to join, and it has missing data for some of the years or countries, I don't want the combined data to throw away all the rows from the main big dataset that don't match! I still want those! 

*([Look at this for a real life example](https://stats.andrewheiss.com/canary-ngos/01_get-merge-data.html#final_clean_combined_data): I create a dataset I name `panel_skeleton` that is just all the combinations of countries and years (Afghanistan 1990, Afghanistan 1991, etc.), and then I bring in all sorts of other datasets that match the same countries and years. When there aren't matches, nothing in the skeleton gets thrown away—R just adds missing values instead.)*

<div class="puzzle">

**Your turn**: Create a new dataset named `combined_data` that uses `left_join()` to merge `national_data_2019` and `national_libraries_2019`.

</div>

{{% learnr url="https://andrewheiss.shinyapps.io/datavizm20_12-joining-5/" id="learnr-12-lesson-joining5" %}}

Left joining is also often surprisingly helpful for recoding lots of variables. Right now in our fake national data, we have a column for state, but it would be nice if we could have a column for region so we could facet or fill or color by region in a plot. Hunting around on the internet, you find this dataset that has a column for state and a column for abbreviations:


```r
state_regions
```

```
## # A tibble: 51 x 2
##    region    state
##    <chr>     <chr>
##  1 West      AK   
##  2 South     AL   
##  3 South     AR   
##  4 West      AZ   
##  5 West      CA   
##  6 West      CO   
##  7 Northeast CT   
##  8 South     DC   
##  9 South     DE   
## 10 South     FL   
## # … with 41 more rows
```

<div class="puzzle">

**Your turn**: Create a new dataset named `national_data_with_region` that uses `left_join()` to combine `national_data_2019` with `state_regions`.

</div>

{{% learnr url="https://andrewheiss.shinyapps.io/datavizm20_12-joining-3/" id="learnr-12-lesson-joining3" %}}

Because `left_join()` only keeps rows from the second dataset that match the first, we don't actually bring in all 50 rows from the `state_regions` data—only the rows that match the first dataset (`national_data_2019`) come over. We could have done with if some massive recoding (`mutate(region = ifelse(state == "GA" | state == "NC", "South", ifelse(state == "CO"), "West", NA))`), but that's awful. Left joining is far easier here.

You can also join by multiple columns. So far we've been working with just `national_data_2019`, but if you look at `national_data`, you'll see there are rows for different years across these states:


```r
national_data
```

```
## # A tibble: 9 x 5
##   state  year unemployment inflation population
##   <chr> <dbl>        <dbl>     <dbl>      <dbl>
## 1 GA     2018          5         2          100
## 2 GA     2019          5.3       1.8        200
## 3 GA     2020          5.2       2.5        300
## 4 NC     2018          6.1       1.8        350
## 5 NC     2019          5.9       1.6        375
## 6 NC     2020          5.3       1.8        400
## 7 CO     2018          4.7       2.7        200
## 8 CO     2019          4.4       2.6        300
## 9 CO     2020          5.1       2.5        400
```

Previously, we've been specifying the ID column with `by = "state"`, but now we have two ID columns: `state` and `year`. We can specify both with `by = c("state", "year")`.

<div class="puzzle">

**Your turn**: Create a new dataset named `national_data_combined` that uses `left_join()` to combine `national_data` with `national_libraries` by state and year.

</div>

{{% learnr url="https://andrewheiss.shinyapps.io/datavizm20_12-joining-4/" id="learnr-12-lesson-joining4" %}}

If one dataset has things like state and year, but another only has state, `left_join()` will still work, but it will only join where the state is the same. For instance, here's what happens when we join the region data to the yearly national data:


```r
national_data_with_region <- national_data %>% 
  left_join(state_regions, by = "state")
national_data_with_region
```

```
## # A tibble: 9 x 6
##   state  year unemployment inflation population region
##   <chr> <dbl>        <dbl>     <dbl>      <dbl> <chr> 
## 1 GA     2018          5         2          100 South 
## 2 GA     2019          5.3       1.8        200 South 
## 3 GA     2020          5.2       2.5        300 South 
## 4 NC     2018          6.1       1.8        350 South 
## 5 NC     2019          5.9       1.6        375 South 
## 6 NC     2020          5.3       1.8        400 South 
## 7 CO     2018          4.7       2.7        200 West  
## 8 CO     2019          4.4       2.6        300 West  
## 9 CO     2020          5.1       2.5        400 West
```

The "South" region gets added to every row where the state is "GA" and "NC", even though those rows only appear once in `state_regions`. `left_join()` will still match all the values even if states are repeated. Magic!

## Common column names

So far, the column names in both datasets have been the same, which has greatly simplified life. In fact, if the columns have the same name, we can technically leave out the `by` argument and R will guess:


```r
national_data %>% 
  left_join(national_libraries)
```

```
## Joining, by = c("state", "year")
```

```
## # A tibble: 9 x 7
##   state  year unemployment inflation population libraries schools
##   <chr> <dbl>        <dbl>     <dbl>      <dbl>     <dbl>   <dbl>
## 1 GA     2018          5         2          100        NA      NA
## 2 GA     2019          5.3       1.8        200        NA      NA
## 3 GA     2020          5.2       2.5        300        NA      NA
## 4 NC     2018          6.1       1.8        350       200     610
## 5 NC     2019          5.9       1.6        375       210     590
## 6 NC     2020          5.3       1.8        400       220     530
## 7 CO     2018          4.7       2.7        200       230     470
## 8 CO     2019          4.4       2.6        300       240     440
## 9 CO     2020          5.1       2.5        400       270     510
```

It's good practice to be specific about the columns you want and actually use `by`, but I will often run `left_join()` without it and then copy the message that it generates ("`by = c("state", "year")`") and paste it into my code. 

But what if the column names don't match? Let's rename the state column in our state/region table for fun:


```r
state_regions_different <- state_regions %>% 
  rename(ST = state)
state_regions_different
```

```
## # A tibble: 51 x 2
##    region    ST   
##    <chr>     <chr>
##  1 West      AK   
##  2 South     AL   
##  3 South     AR   
##  4 West      AZ   
##  5 West      CA   
##  6 West      CO   
##  7 Northeast CT   
##  8 South     DC   
##  9 South     DE   
## 10 South     FL   
## # … with 41 more rows
```

Now watch what happens when we try to join the datasets:


```r
national_data %>% 
  left_join(state_regions_different)
```

```
## Error: `by` must be supplied when `x` and `y` have no common variables.
## ℹ use by = character()` to perform a cross-join.
```

There are no common variables, so we get an error. The `state` and `ST` columns really are common variables, but R doesn't know that.

We have two options:

1. Rename one of the columns so it matches (either change `state` to `ST` or change `ST` to `state`)
2. Tell `left_join()` which columns are the same

We can do option two by modifying the `by` argument like so:


```r
national_data %>% 
  left_join(state_regions_different, by = c("state" = "ST"))
```

```
## # A tibble: 9 x 6
##   state  year unemployment inflation population region
##   <chr> <dbl>        <dbl>     <dbl>      <dbl> <chr> 
## 1 GA     2018          5         2          100 South 
## 2 GA     2019          5.3       1.8        200 South 
## 3 GA     2020          5.2       2.5        300 South 
## 4 NC     2018          6.1       1.8        350 South 
## 5 NC     2019          5.9       1.6        375 South 
## 6 NC     2020          5.3       1.8        400 South 
## 7 CO     2018          4.7       2.7        200 West  
## 8 CO     2019          4.4       2.6        300 West  
## 9 CO     2020          5.1       2.5        400 West
```


## `right_join()`

Once again, <a href="https://github.com/gadenbuie/tidyexplain#right-join" target="_blank">go to this page in a new tab</a> and watch the animation.

`right_join()` works exactly like `left_join()`, but in reverse. The *second* dataset is the base data. Any rows in the second dataset that don't match in the first will be kept, and any rows from the first that don't match will get thrown away.

Watch what happens if we right join `national_data` and `state_regions`:


```r
national_data %>% 
  right_join(state_regions, by = "state")
```

```
## # A tibble: 57 x 6
##    state  year unemployment inflation population region
##    <chr> <dbl>        <dbl>     <dbl>      <dbl> <chr> 
##  1 GA     2018          5         2          100 South 
##  2 GA     2019          5.3       1.8        200 South 
##  3 GA     2020          5.2       2.5        300 South 
##  4 NC     2018          6.1       1.8        350 South 
##  5 NC     2019          5.9       1.6        375 South 
##  6 NC     2020          5.3       1.8        400 South 
##  7 CO     2018          4.7       2.7        200 West  
##  8 CO     2019          4.4       2.6        300 West  
##  9 CO     2020          5.1       2.5        400 West  
## 10 AK       NA         NA        NA           NA West  
## # … with 47 more rows
```

Yikes. R kept all the rows in `state_regions`, brought in the columns from `national_data` and filled most of the new columns with `NA`, and then repeated Colorado (and NC and GA) three times for each of the years from `national_data`. That's a mess.

If we reverse the order, we'll get the correct merged data:


```r
state_regions %>% 
  right_join(national_data, by = "state")
```

```
## # A tibble: 9 x 6
##   region state  year unemployment inflation population
##   <chr>  <chr> <dbl>        <dbl>     <dbl>      <dbl>
## 1 West   CO     2018          4.7       2.7        200
## 2 West   CO     2019          4.4       2.6        300
## 3 West   CO     2020          5.1       2.5        400
## 4 South  GA     2018          5         2          100
## 5 South  GA     2019          5.3       1.8        200
## 6 South  GA     2020          5.2       2.5        300
## 7 South  NC     2018          6.1       1.8        350
## 8 South  NC     2019          5.9       1.6        375
## 9 South  NC     2020          5.3       1.8        400
```

I rarely use `right_join()` because I find it more intuitive to just use `left_join()` since in my head, I'm taking a dataset and stacking columns onto the end of it. If you want to right join instead, neat—just remember to order things correctly.

