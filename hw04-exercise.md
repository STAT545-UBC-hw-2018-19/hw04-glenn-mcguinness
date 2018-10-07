STAT 545A - Homework 4: Tidy data and joins
================
Glenn McGuinness

-   [Introduction](#introduction)
-   [Tasks](#tasks)
    -   [Task 1: Data Reshaping - Activity 2](#task-1-data-reshaping---activity-2)
    -   [Task 2: Joins - Activity 1](#task-2-joins---activity-1)

Introduction
------------

The goal of this assignment is to use data wrangling to solve realistic problems. One of the new tools to be used in this assignement will be data frame joins and reshaping. To this end, one data reshaping and one join prompt wil be taken from the given in the assignment.

Tasks
-----

### Task 1: Data Reshaping - Activity 2

This activity is described as follows:

> Make a tibble with one row per year and columns for life expectancy for two or more countries.
>
> -   Use knitr::kable() to make this table look pretty in your rendered homework.
>
> -   Take advantage of this new data shape to scatterplot life expectancy for one country against that of another.

I will make the data for this assignment from the gapminder dataset. Forthis task, I will look at the life expectancy of `Canada`, the `United States`, the `United Kingdom`, and `China`.

``` r
suppressPackageStartupMessages(library(gapminder))

# Make the dataset for this assignment
gapLifeExp = gapminder %>%
    filter(country %in% c("Canada", "United States", "United Kingdom", "China")) %>%
    select(country, year, lifeExp)
```

Now that I have the dataset prepared I can create the table. To create the desired table for display, I will need to reshape the data to put each of the countries into a column. This can be performed by the `spread()` function, which will make the chosen keys, countries, into columns and place the values, life expectancy, into the rows. I will store this in a variable with a descriptive name `gapLifeExpSpread`, so I can use it to generate a scatterplot later.

``` r
gapLifeExpSpread = gapLifeExp %>%
    spread(key = country, value = lifeExp)
```

To create the table, I will be using the `kable` package. This package is intended to make tables more readable.

``` r
gapLifeExpSpread %>%
    knitr::kable(caption = 'Life Expectancy by Year', 
                 format = 'markdown', 
                 booktabs = TRUE, 
                 longtable = TRUE)
```

|  year|  Canada|     China|  United Kingdom|  United States|
|-----:|-------:|---------:|---------------:|--------------:|
|  1952|  68.750|  44.00000|          69.180|         68.440|
|  1957|  69.960|  50.54896|          70.420|         69.490|
|  1962|  71.300|  44.50136|          70.760|         70.210|
|  1967|  72.130|  58.38112|          71.360|         70.760|
|  1972|  72.880|  63.11888|          72.010|         71.340|
|  1977|  74.210|  63.96736|          72.760|         73.380|
|  1982|  75.760|  65.52500|          74.040|         74.650|
|  1987|  76.860|  67.27400|          75.007|         75.020|
|  1992|  77.950|  68.69000|          76.420|         76.090|
|  1997|  78.610|  70.42600|          77.218|         76.810|
|  2002|  79.770|  72.02800|          78.471|         77.310|
|  2007|  80.653|  72.96100|          79.425|         78.242|

This table is can now easily be used to make a scatter plot of the life expectancy of `Canada` vs that of the `China`, as they are in separate columns. I will also add a second order polynomial fit to give a sense of the trend over time.

``` r
gapLifeExpSpread %>%
    ggplot(aes(x = China, y = Canada)) + 
    geom_point(size = 3) +
    stat_smooth(method = "gam", formula = y ~ poly(x,2), se = FALSE) +
    theme_bw()
```

![](hw04-exercise_files/figure-markdown_github/task1MakePlot-1.png)

Note, the x and y axis do not have the same ranges. `China` experienced a much larger increase in life expectancy over this period than `Canada`, so it makes the plot much clearer if the axis have a different range of life expectancies.

From this plot, it is clear that `China` increased it's life expectancy from below fifty to above sixty at a much more rapid rate than `Canada`. After `China` had a life expectancy of low sixties, it's rate of growth slowed in relation to that of `Canada`.

### Task 2: Joins - Activity 1

This activity is described as follows:

> Create a second data frame, complementary to Gapminder. Join this with (part of) Gapminder using a dplyr join function and make some observations about the process and \\ result. Explore the different types of joins. Examples of a second data frame you could build: \\ - One row per country, a country variable and one or more variables with extra info, such as language spoken, NATO membership, national animal, or capitol city. \\ - One row per continent, a continent variable and one or more variables with extra info, such as northern versus southern hemisphere. \\
