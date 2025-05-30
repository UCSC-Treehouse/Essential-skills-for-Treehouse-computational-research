# Chapter 10, Exploratory Data Analysis


This chapter will show you how to use visualization and transformation
to explore your data in a systematic way, a task that statisticians call
**exploratory data analysis**, or **EDA** for short. EDA is an iterative
cycle. You:

1.  Generate questions about your data.

2.  Search for answers by visualizing, transforming, and modelling your
    data.

3.  Use what you learn to refine your questions and/or generate new
    questions.

Data cleaning is just one application of EDA: you ask questions about
whether your data meets your expectations or not. To do data cleaning,
you’ll need to deploy all the tools of EDA: visualization,
transformation, and modelling.

------------------------------------------------------------------------

## Prerequisites

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.5.2     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ✔ purrr     1.0.4     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

------------------------------------------------------------------------

## Questions to Think About

Your goal during EDA is to develop an understanding of your data. The
easiest way to do this is to use questions as tools to guide your
investigation. When you ask a question, the question focuses your
attention on a specific part of your dataset and helps you decide which
graphs, models, or transformations to make. There is no rule about which
questions you should ask to guide your research. However, two types of
questions will always be useful for making discoveries within your data.
You can loosely word these questions as:

1.  What type of variation occurs within my variables?

2.  What type of covariation occurs between my variables?

------------------------------------------------------------------------

## Variation

Variation is the tendency of the values of a variable to change from
measurement to measurement. You can see variation easily in real life;
if you measure any continuous variable twice, you will get two different
results. Variables can also vary if you measure across different
subjects (e.g., the eye colors of different people) or at different
times (e.g., the energy levels of an electron at different moments).
Every variable has its own pattern of variation, which can reveal
interesting information about how it varies between measurements on the
same observation as well as across observations.

We’ll start our exploration by visualizing the distribution of weights
(carat) of ~54,000 diamonds from the diamonds dataset. Since carat is a
numerical variable, we can use a histogram:

``` r
ggplot(diamonds, aes(x = carat)) +
  geom_histogram(binwidth = 0.5)
```

![](Chapter_10_Instructions_files/figure-commonmark/distribution%20of%20diamond%20weights-1.png)

Now that you can visualize variation, what should you look for in your
plots? And what type of follow-up questions should you ask?

------------------------------------------------------------------------

## Typical Values

In both bar charts and histograms, tall bars show the common values of a
variable, and shorter bars show less-common values. Places that do not
have bars reveal values that were not seen in your data. To turn this
information into useful questions, look for anything unexpected:

- Which values are the most common? Why?

- Which values are rare? Why? Does that match your expectations?

- Can you see any unusual patterns? What might explain them?

Let’s take a look at the distribution of carat for smaller diamonds.

``` r
smaller <- diamonds |> 
  filter(carat < 3)

ggplot(smaller, aes(x = carat)) +
  geom_histogram(binwidth = 0.01)
```

![](Chapter_10_Instructions_files/figure-commonmark/distribution%20of%20small%20diamond%20weights-1.png)

This histogram suggests several interesting questions:

- Why are there more diamonds at whole carats and common fractions of
  carats?

- Why are there more diamonds slightly to the right of each peak than
  there are slightly to the left of each peak?

Visualizations can also reveal clusters, which suggest that subgroups
exist in your data. To understand the subgroups, ask:

- How are the observations within each subgroup similar to each other?

- How are the observations in separate clusters different from each
  other?

- How can you explain or describe the clusters?

- Why might the appearance of clusters be misleading?

------------------------------------------------------------------------

## Unusual Values

Outliers are observations that are unusual; data points that don’t seem
to fit the pattern. Sometimes outliers are data entry errors, sometimes
they are simply values at the extremes that happened to be observed in
this data collection, and other times they suggest important new
discoveries. When you have a lot of data, outliers are sometimes
difficult to see in a histogram. For example, take the distribution of
the y variable from the diamonds dataset. The only evidence of outliers
is the unusually wide limits on the x-axis.

``` r
ggplot(diamonds, aes(x = y)) + 
  geom_histogram(binwidth = 0.5)
```

![](Chapter_10_Instructions_files/figure-commonmark/variation%20of%20y%20values%20in%20diamond%20dataset-1.png)

There are so many observations in the common bins that the rare bins are
very short, making it very difficult to see them (although maybe if you
stare intently at 0 you’ll spot something). To make it easy to see the
unusual values, we need to zoom to small values of the y-axis with
coord_cartesian():

``` r
ggplot(diamonds, aes(x = y)) + 
  geom_histogram(binwidth = 0.5) +
  coord_cartesian(ylim = c(0, 50))
```

![](Chapter_10_Instructions_files/figure-commonmark/zoom%20in%20to%20small%20values%20on%20y%20axis-1.png)

coord_cartesian() also has an xlim() argument for when you need to zoom
into the x-axis. ggplot2 also has xlim() and ylim() functions that work
slightly differently: they throw away the data outside the limits.

This allows us to see that there are three unusual values: 0, ~30, and
~60. We pluck them out with dplyr:

``` r
unusual <- diamonds |> 
  filter(y < 3 | y > 20) |> 
  select(price, x, y, z) |>
  arrange(y)
unusual
```

The y variable measures one of the three dimensions of these diamonds,
in mm. We know that diamonds can’t have a width of 0mm, so these values
must be incorrect. By doing EDA, we have discovered missing data that
was coded as 0, which we never would have found by simply searching for
NAs. Going forward we might choose to re-code these values as NAs in
order to prevent misleading calculations. We might also suspect that
measurements of 32mm and 59mm are implausible: those diamonds are over
an inch long, but don’t cost hundreds of thousands of dollars!

------------------------------------------------------------------------

## Exercises pt 1 of 5

1.  Explore the distribution of each of the x, y, and z variables in
    diamonds. What do you learn? Think about a diamond and how you might
    decide which dimension is the length, width, and depth.

2.  Explore the distribution of price. Do you discover anything unusual
    or surprising? (Hint: Carefully think about the binwidth and make
    sure you try a wide range of values.)

3.  How many diamonds are 0.99 carat? How many are 1 carat? What do you
    think is the cause of the difference?

4.  Compare and contrast coord_cartesian() vs. xlim() or ylim() when
    zooming in on a histogram. What happens if you leave binwidth unset?
    What happens if you try and zoom so only half a bar shows?

------------------------------------------------------------------------

## Unusual Values

If you’ve encountered unusual values in your dataset, and simply want to
move on to the rest of your analysis, you have two options.

1.  Drop the entire row with the strange values:

``` r
diamonds2 <- diamonds |> 
  filter(between(y, 3, 20))
```

This option is not recommended because one invalid value doesn’t imply
that all the other values for that observation are also invalid.

OR YOU CAN…

2.  Instead, we recommend replacing the unusual values with missing
    values. The easiest way to do this is to use mutate() to replace the
    variable with a modified copy. You can use the if_else() function to
    replace unusual values with NA:

``` r
diamonds2 <- diamonds |> 
  mutate(y = if_else(y < 3 | y > 20, NA, y))
```

It’s not obvious where you should plot missing values, so ggplot2
doesn’t include them in the plot, but it does warn that they’ve been
removed:

``` r
ggplot(diamonds2, aes(x = x, y = y)) + 
  geom_point()
```

To suppress that warning, set na.rm = TRUE:

``` r
ggplot(diamonds2, aes(x = x, y = y)) + 
  geom_point(na.rm = TRUE)
```

Other times you want to understand what makes observations with missing
values different to observations with recorded values. For example, in
nycflights13::flights, missing values in the dep_time variable indicate
that the flight was cancelled. So you might want to compare the
scheduled departure times for cancelled and non-cancelled times. You can
do this by making a new variable, using is.na() to check if dep_time is
missing.

``` r
nycflights13::flights |> 
  mutate(
    cancelled = is.na(dep_time),
    sched_hour = sched_dep_time %/% 100,
    sched_min = sched_dep_time %% 100,
    sched_dep_time = sched_hour + (sched_min / 60)
  ) |> 
  ggplot(aes(x = sched_dep_time)) + 
  geom_freqpoly(aes(color = cancelled), binwidth = 1/4)
```

![](Chapter_10_Instructions_files/figure-commonmark/cancelled%20and%20non-cancelled%20flights-1.png)

However this plot isn’t great because there are many more non-cancelled
flights than cancelled flights.

------------------------------------------------------------------------

## Exercises pt 2 of 5

1.  What happens to missing values in a histogram? What happens to
    missing values in a bar chart? Why is there a difference in how
    missing values are handled in histograms and bar charts?

2.  What does na.rm = TRUE do in mean() and sum()?

3.  Recreate the frequency plot of scheduled_dep_time colored by whether
    the flight was cancelled or not. Also facet by the cancelled
    variable. Experiment with different values of the scales variable in
    the faceting function to mitigate the effect of more non-cancelled
    flights than cancelled flights.

------------------------------------------------------------------------

## Covariation

If variation describes the behavior *within* a variable, covariation
describes the behavior *between* variables. **Covariation** is the
tendency for the values of two or more variables to vary together in a
related way. The best way to spot covariation is to visualize the
relationship between two or more variables.

------------------------------------------------------------------------

## A Categorical and a Numerical Variable

Let’s explore how the price of a diamond varies with its quality
(measured by cut) using geom_freqpoly():

``` r
ggplot(diamonds, aes(x = price)) + 
  geom_freqpoly(aes(color = cut), binwidth = 500, linewidth = 0.75)
```

![](Chapter_10_Instructions_files/figure-commonmark/price%20of%20diamond%20v%20cut%20quality-1.png)

Note that ggplot2 uses an ordered color scale for cut because it’s
defined as an ordered factor variable in the data.

The default appearance of geom_freqpoly() is not that useful here
because the height, determined by the overall count, differs so much
across cuts, making it hard to see the differences in the shapes of
their distributions.

To make the comparison easier we need to swap what is displayed on the
y-axis. Instead of displaying count, we’ll display the **density**,
which is the count standardized so that the area under each frequency
polygon is one.

``` r
ggplot(diamonds, aes(x = price, y = after_stat(density))) + 
  geom_freqpoly(aes(color = cut), binwidth = 500, linewidth = 0.75)
```

![](Chapter_10_Instructions_files/figure-commonmark/price%20of%20diamond%20v%20density-1.png)

Note that we’re mapping the density to y, but since density is not a
variable in the diamonds dataset, we need to first calculate it. We use
the after_stat() function to do so.

There’s something rather surprising about this plot - it appears that
fair diamonds (the lowest quality) have the highest average price! But
maybe that’s because frequency polygons are a little hard to interpret -
there’s a lot going on in this plot.

A visually simpler plot for exploring this relationship is using
side-by-side boxplots.

``` r
ggplot(diamonds, aes(x = cut, y = price)) +
  geom_boxplot()
```

![](Chapter_10_Instructions_files/figure-commonmark/price%20v%20diamond%20density%20boxplot-1.png)

We see much less information about the distribution, but the boxplots
are much more compact so we can more easily compare them (and fit more
on one plot). It supports the counter-intuitive finding that better
quality diamonds are typically cheaper! In the exercises, you’ll be
challenged to figure out why.

cut is an ordered factor: fair is worse than good, which is worse than
very good and so on. Many categorical variables don’t have such an
intrinsic order, so you might want to reorder them to make a more
informative display. One way to do that is with fct_reorder(). For
example, take the class variable in the mpg dataset. You might be
interested to know how highway mileage varies across classes:

``` r
ggplot(mpg, aes(x = class, y = hwy)) +
  geom_boxplot()
```

![](Chapter_10_Instructions_files/figure-commonmark/highway%20mileage%20variation%20across%20classes-1.png)

To make the trend easier to see, we can reorder class based on the
median value of hwy:

``` r
ggplot(mpg, aes(x = fct_reorder(class, hwy, median), y = hwy)) +
  geom_boxplot()
```

![](Chapter_10_Instructions_files/figure-commonmark/reorder%20boxplots%20off%20median%20values-1.png)

If you have long variable names, geom_boxplot() will work better if you
flip it 90°. You can do that by exchanging the x and y aesthetic
mappings.

``` r
ggplot(mpg, aes(x = hwy, y = fct_reorder(class, hwy, median))) +
  geom_boxplot()
```

![](Chapter_10_Instructions_files/figure-commonmark/flip%20x%20y%20axes-1.png)

------------------------------------------------------------------------

## Exercises pt 3 of 5

1.  Use what you’ve learned to improve the visualization of the
    departure times of cancelled vs. non-cancelled flights.

2.  Based on EDA, what variable in the diamonds dataset appears to be
    most important for predicting the price of a diamond? How is that
    variable correlated with cut? Why does the combination of those two
    relationships lead to lower quality diamonds being more expensive?

3.  Instead of exchanging the x and y variables, add coord_flip() as a
    new layer to the vertical boxplot to create a horizontal one. How
    does this compare to exchanging the variables?

4.  One problem with boxplots is that they were developed in an era of
    much smaller datasets and tend to display a prohibitively large
    number of “outlying values”. One approach to remedy this problem is
    the letter value plot. Install the lvplot package, and try using
    geom_lv() to display the distribution of price vs. cut. What do you
    learn? How do you interpret the plots?

5.  Create a visualization of diamond prices vs. a categorical variable
    from the diamonds dataset using geom_violin(), then a faceted
    geom_histogram(), then a colored geom_freqpoly(), and then a colored
    geom_density(). Compare and contrast the four plots. What are the
    pros and cons of each method of visualizing the distribution of a
    numerical variable based on the levels of a categorical variable?

6.  If you have a small dataset, it’s sometimes useful to use
    geom_jitter() to avoid overplotting to more easily see the
    relationship between a continuous and categorical variable. The
    ggbeeswarm package provides a number of methods similar to
    geom_jitter(). List them and briefly describe what each one does.

------------------------------------------------------------------------

## Two Categorical Variables

To visualize the covariation between categorical variables, you’ll need
to count the number of observations for each combination of levels of
these categorical variables. One way to do that is to rely on the
built-in geom_count():

``` r
ggplot(diamonds, aes(x = cut, y = color)) +
  geom_count()
```

![](Chapter_10_Instructions_files/figure-commonmark/count%20categorical%20values-1.png)

The size of each circle in the plot displays how many observations
occurred at each combination of values. Covariation will appear as a
strong correlation between specific x values and specific y values.

Another approach for exploring the relationship between these variables
is computing the counts with dplyr:

``` r
diamonds |> 
  count(color, cut)
```

Then visualize with geom_tile() and the fill aesthetic:

``` r
diamonds |> 
  count(color, cut) |>  
  ggplot(aes(x = color, y = cut)) +
  geom_tile(aes(fill = n))
```

![](Chapter_10_Instructions_files/figure-commonmark/diamonds%20tile%20plot-1.png)

If the categorical variables are unordered, you might want to use the
seriation package to simultaneously reorder the rows and columns in
order to more clearly reveal interesting patterns. For larger plots, you
might want to try the heatmaply package, which creates interactive
plots.

------------------------------------------------------------------------

## Exercises pt 4 of 5

1.  How could you rescale the count dataset above to more clearly show
    the distribution of cut within color, or color within cut?

2.  What different data insights do you get with a segmented bar chart
    if color is mapped to the x aesthetic and cut is mapped to the fill
    aesthetic? Calculate the counts that fall into each of the segments.

3.  Use geom_tile() together with dplyr to explore how average flight
    departure delays vary by destination and month of year. What makes
    the plot difficult to read? How could you improve it?

------------------------------------------------------------------------

## Two Numerical Variables

You’ve already seen one great way to visualize the covariation between
two numerical variables: draw a scatterplot with geom_point(). You can
see covariation as a pattern in the points. For example, you can see a
positive relationship between the carat size and price of a diamond:
diamonds with more carats have a higher price. The relationship is
exponential.

``` r
ggplot(smaller, aes(x = carat, y = price)) +
  geom_point()
```

![](Chapter_10_Instructions_files/figure-commonmark/carat%20v%20price%20diamonds-1.png)

Scatterplots become less useful as the size of your dataset grows,
because points begin to overplot, and pile up into areas of uniform
black, making it hard to judge differences in the density of the data
across the 2-dimensional space as well as making it hard to spot the
trend. You’ve already seen one way to fix the problem: using the alpha
aesthetic to add transparency.

``` r
ggplot(smaller, aes(x = carat, y = price)) + 
  geom_point(alpha = 1 / 100)
```

![](Chapter_10_Instructions_files/figure-commonmark/carat%20v%20price%20diamonds%20transparent-1.png)

But using transparency can be challenging for very large datasets.
Another solution is to use bin. Previously you used geom_histogram() and
geom_freqpoly() to bin in one dimension. Now you’ll learn how to use
geom_bin2d() and geom_hex() to bin in two dimensions.

geom_bin2d() and geom_hex() divide the coordinate plane into 2d bins and
then use a fill color to display how many points fall into each bin.
geom_bin2d() creates rectangular bins. geom_hex() creates hexagonal
bins. You will need to install the hexbin package to use geom_hex().

``` r
ggplot(smaller, aes(x = carat, y = price)) +
  geom_bin2d()
```

![](Chapter_10_Instructions_files/figure-commonmark/carat%20v%20price%20diamonds%202d%20bin%20and%20hexagon%20plot-1.png)

``` r
# install.packages("hexbin")
ggplot(smaller, aes(x = carat, y = price)) +
  geom_hex()
```

![](Chapter_10_Instructions_files/figure-commonmark/carat%20v%20price%20diamonds%202d%20bin%20and%20hexagon%20plot-2.png)

Another option is to bin one continuous variable so it acts like a
categorical variable. Then you can use one of the techniques for
visualizing the combination of a categorical and a continuous variable
that you learned about. For example, you could bin carat and then for
each group, display a boxplot:

``` r
ggplot(smaller, aes(x = carat, y = price)) + 
  geom_boxplot(aes(group = cut_width(carat, 0.1)))
```

![](Chapter_10_Instructions_files/figure-commonmark/bin%20carat%20v%20price%20diamonds%20boxplot-1.png)

cut_width(x, width), as used above, divides x into bins of width width.
By default, boxplots look roughly the same (apart from number of
outliers) regardless of how many observations there are, so it’s
difficult to tell that each boxplot summarizes a different number of
points. One way to show that is to make the width of the boxplot
proportional to the number of points with varwidth = TRUE.

------------------------------------------------------------------------

## Exercises pt 5 of 5

1.  Instead of summarizing the conditional distribution with a boxplot,
    you could use a frequency polygon. What do you need to consider when
    using cut_width() vs. cut_number()? How does that impact a
    visualization of the 2d distribution of carat and price?

2.  Visualize the distribution of carat, partitioned by price.

3.  How does the price distribution of very large diamonds compare to
    small diamonds? Is it as you expect, or does it surprise you?

4.  Combine two of the techniques you’ve learned to visualize the
    combined distribution of cut, carat, and price.

5.  Two dimensional plots reveal outliers that are not visible in one
    dimensional plots. For example, some points in the following plot
    have an unusual combination of x and y values, which makes the
    points outliers even though their x and y values appear normal when
    examined separately. Why is a scatterplot a better display than a
    binned plot for this case?

``` r
diamonds |> 
  filter(x >= 4) |> 
  ggplot(aes(x = x, y = y)) +
  geom_point() +
  coord_cartesian(xlim = c(4, 11), ylim = c(4, 11))
```

![](Chapter_10_Instructions_files/figure-commonmark/ch%2010%20exercises%20pt%205%20#5-1.png)

6.  Instead of creating boxes of equal width with cut_width(), we could
    create boxes that contain roughly equal number of points with
    cut_number(). What are the advantages and disadvantages of this
    approach?

``` r
ggplot(smaller, aes(x = carat, y = price)) + 
  geom_boxplot(aes(group = cut_number(carat, 20)))
```

![](Chapter_10_Instructions_files/figure-commonmark/ch%2010%20exercises%20pt%205%20#6-1.png)

------------------------------------------------------------------------

## Patterns and Models

If a systematic relationship exists between two variables it will appear
as a pattern in the data. If you spot a pattern, ask yourself:

- Could this pattern be due to coincidence (i.e. random chance)?

- How can you describe the relationship implied by the pattern?

- How strong is the relationship implied by the pattern?

- What other variables might affect the relationship?

- Does the relationship change if you look at individual subgroups of
  the data?

Patterns in your data provide clues about relationships, i.e., they
reveal covariation. If you think of variation as a phenomenon that
creates uncertainty, covariation is a phenomenon that reduces it. If two
variables covary, you can use the values of one variable to make better
predictions about the values of the second. If the covariation is due to
a causal relationship (a special case), then you can use the value of
one variable to control the value of the second.

Models are a tool for extracting patterns out of data. For example,
consider the diamonds data. It’s hard to understand the relationship
between cut and price, because cut and carat, and carat and price are
tightly related.

The following code fits a model that predicts price from carat and then
computes the residuals (the difference between the predicted value and
the actual value). The residuals give us a view of the price of the
diamond, once the effect of carat has been removed. Note that instead of
using the raw values of price and carat, we log transform them first,
and fit a model to the log-transformed values. Then, we exponentiate the
residuals to put them back in the scale of raw prices.

``` r
library(tidymodels)
```

    ── Attaching packages ────────────────────────────────────── tidymodels 1.3.0 ──

    ✔ broom        1.0.8     ✔ rsample      1.3.0
    ✔ dials        1.4.0     ✔ tune         1.3.0
    ✔ infer        1.0.8     ✔ workflows    1.2.0
    ✔ modeldata    1.4.0     ✔ workflowsets 1.1.0
    ✔ parsnip      1.3.1     ✔ yardstick    1.3.2
    ✔ recipes      1.3.0     

    ── Conflicts ───────────────────────────────────────── tidymodels_conflicts() ──
    ✖ scales::discard() masks purrr::discard()
    ✖ dplyr::filter()   masks stats::filter()
    ✖ recipes::fixed()  masks stringr::fixed()
    ✖ dplyr::lag()      masks stats::lag()
    ✖ yardstick::spec() masks readr::spec()
    ✖ recipes::step()   masks stats::step()

``` r
#| eval: false

diamonds <- diamonds |>
  mutate(
    log_price = log(price),
    log_carat = log(carat)
  )

diamonds_fit <- linear_reg() |>
  fit(log_price ~ log_carat, data = diamonds)

diamonds_aug <- augment(diamonds_fit, new_data = diamonds) |>
  mutate(.resid = exp(.resid))

ggplot(diamonds_aug, aes(x = carat, y = .resid)) + 
  geom_point()
```

![](Chapter_10_Instructions_files/figure-commonmark/log%20transform%20and%20model%20price%20v%20carat%20diamond-1.png)

Once you’ve removed the strong relationship between carat and price, you
can see what you expect in the relationship between cut and price:
relative to their size, better quality diamonds are more expensive.

``` r
ggplot(diamonds_aug, aes(x = cut, y = .resid)) + 
  geom_boxplot()
```

![](Chapter_10_Instructions_files/figure-commonmark/remove%20carat%20v%20price%20to%20focus%20on%20cut%20v%20price-1.png)
