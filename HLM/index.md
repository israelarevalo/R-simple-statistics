## Before we start

You can find this guide and more on my [github
page](https://github.com/israelarevalo/R-simple-statistics-tutorials).

This guide was designed as a friendly approach to learn how to conduct
statistical analysis within the R environment. R is a powerful
open-source tool that allows for great flexibility and customization of
when conducting data wrangling (cleaning), analysis, interpretation, and
reporting. Did I mention its open-source? This not only means that it is
free, but the entire code used to develop this exceptional language is
entirely available for you to view (and even suggest changes) online.

Several assumptions about the user will be made within this guide.

1.  You have installed R and an IDE such as RStudio or Visual Studio
    Code on your computer
2.  You have a dataset to work with

If these assumptions are not met, here are a few guides to reference
that will get you up to speed

-   [Installing
    RStudio](https://rstudio-education.github.io/hopr/starting.html)
-   [Exporting SPSS dataset to
    .CSV](https://www.ibm.com/docs/en/spss-statistics/beta?topic=files-exporting-datasets)
-   [Exporting STATA dataset to
    .csv](https://stats.oarc.ucla.edu/stata/faq/how-do-i-export-stata-dta-files-to-comma-separated-files/)
-   [Exporting Excel file to
    .csv](https://support.microsoft.com/en-us/office/import-or-export-text-txt-or-csv-files-5250ac4c-663c-47ce-937b-339e391393ba)

## Mixed Effects Models - What are they and how can we use them?

In educational research, it is common to collect data from multiple
participants or classrooms. These data often exhibit nested or
hierarchical structures, where individuals are nested within
higher-level units (e.g., students within classrooms, classrooms within
schools). In such cases, traditional statistical models assume
independence of observations, which may lead to biased estimates of the
parameters.

Mixed effects models, also known as hierarchical linear models (HLM),
are statistical models that take into account the nested structure of
the data and allow for the estimation of both fixed and random effects.
Fixed effects are the parameters of interest that are assumed to be
constant across all the individuals or units, while random effects
capture the variability across individuals or units.

Using mixed effects models can improve the accuracy of the estimates,
increase statistical power, and provide a more comprehensive
understanding of the underlying processes in educational research. In
this tutorial, we will use the R programming language to illustrate the
basics of mixed effects modeling. We will use the `lme4` package, which
is one of the most popular packages for fitting mixed effects models in
R.

## Generating our Dataset

Using randomly-generated data, let’s build a fictitious dataset to math
the scenario above and that we will use to run our HLM Model.

> ***Note***
>
> This step is only required for the instructional nature of this
> tutorial. You will **not** need to run this step as you do not need to
> randomly generate your data. Unless you do… in which case, have at it!

In the code block below, we will generate a dataframe consisting of math
scores from students nested within classrooms and their respective SES.
The purpose of this example is to determine whether SES had an effect on
math scores while taking into account the nested structure of the data.

``` r
# Generate sample data
set.seed(1342)
n_classrooms <- 12
n_students <- 10
classroom_means_math <- rnorm(n_classrooms, mean = 50, sd = 5) # random means for each classroom in math scores
classroom_means_ses <- rnorm(n_classrooms, mean = 50, sd = 10) # random means for each classroom in SES
math_score_ctrl <- rnorm(n_classrooms*n_students, mean = rep(classroom_means_math, each = n_students), sd = 10)
math_score_trt <- rnorm(n_classrooms*n_students, mean = rep(classroom_means_math, each = n_students) + 3, sd = 7) # higher mean and smaller SD
math_score <- c(math_score_ctrl, math_score_trt)
ses_ctrl <- rnorm(n_classrooms*n_students, mean = rep(classroom_means_ses, each = n_students), sd = 10)
ses_trt <- rnorm(n_classrooms*n_students, mean = rep(classroom_means_ses, each = n_students) + 5, sd = 5) # higher mean and smaller SD
ses <- c(ses_ctrl, ses_trt)
classroom_id <- rep(1:n_classrooms, each = n_students*2)
student_id <- rep(1:(n_students*2), times = n_classrooms)

# Create data frame
data <- data.frame(math_score, classroom_id, student_id, ses)

# Show first few rows
head(data)
```

    ##   math_score classroom_id student_id      ses
    ## 1   55.99384            1          1 59.01612
    ## 2   38.87901            1          2 59.39918
    ## 3   53.33559            1          3 53.32749
    ## 4   67.59600            1          4 63.88191
    ## 5   35.90531            1          5 51.18091
    ## 6   52.84618            1          6 63.36392

## Exploring our Data

Let’s take a look at simple descriptive statisitcs within our data.
Running the base (summary) command allows us to look at information such
as the minimum, maximum, quartiles, median, and means for each variable
within our dataset. You can further customize this command by adding
further adjustments to the command itself. For example, if we want to
run5 a summary command on just 1 or 2 variables within a dataset
containing many other variables, we can adjust our code to specify which
variables we are interested in by adding a `$` symbol after the name of
the dataset and then specifying the name of the variable (i.e.,
`summary(df$ses`). Below we have run a simple `summary` command to
explore our generated dataset. Since we only generated two variables, we
do not need to specify which variables we are interested in.

``` r
summary(data)
```

    ##    math_score     classroom_id     student_id         ses       
    ##  Min.   :20.82   Min.   : 1.00   Min.   : 1.00   Min.   :23.27  
    ##  1st Qu.:41.00   1st Qu.: 3.75   1st Qu.: 5.75   1st Qu.:50.93  
    ##  Median :49.58   Median : 6.50   Median :10.50   Median :58.34  
    ##  Mean   :49.41   Mean   : 6.50   Mean   :10.50   Mean   :57.87  
    ##  3rd Qu.:56.97   3rd Qu.: 9.25   3rd Qu.:15.25   3rd Qu.:65.21  
    ##  Max.   :74.32   Max.   :12.00   Max.   :20.00   Max.   :86.27

Some summary information may not be necessarily useful (e.g.,
classroom_id and student_id). When analyzing nested data, we can
leverage the `dplyr` package to group students by classroom and then
calculate descriptive data. The `dplyr` package is a powerful tool that
allows us to conduct data wrangling (cleaning) and analysis. In the code
block below, we will use the `dplyr` package to group our students by
classroom and then calculate the number of students per classroom and
the mean and standard deviation of their math scores. Additionally, we
will use the `describeBy` function from the `psych` package to calculate
descriptive statistics for each classroom.

``` r
# Load dplyr and psych package
library(dplyr)
library(psych)

# Group students by classroom and calculate the number of students per classroom
data %>% 
  group_by(classroom_id) %>% 
  summarise(n = n()) %>%
  ungroup()
```

    ## # A tibble: 12 × 2
    ##    classroom_id     n
    ##           <int> <int>
    ##  1            1    20
    ##  2            2    20
    ##  3            3    20
    ##  4            4    20
    ##  5            5    20
    ##  6            6    20
    ##  7            7    20
    ##  8            8    20
    ##  9            9    20
    ## 10           10    20
    ## 11           11    20
    ## 12           12    20

``` r
# Group students by classroom and calculate mean and standard deviation
data %>%
  group_by(classroom_id) %>%
  summarise(mean = mean(math_score), sd = sd(math_score)) %>%
  ungroup()
```

    ## # A tibble: 12 × 3
    ##    classroom_id  mean    sd
    ##           <int> <dbl> <dbl>
    ##  1            1  54.7  8.58
    ##  2            2  41.8 10.4 
    ##  3            3  46.1 10.2 
    ##  4            4  53.1  8.70
    ##  5            5  46.4 13.6 
    ##  6            6  47.2 13.5 
    ##  7            7  57.1  6.75
    ##  8            8  44.0  7.56
    ##  9            9  51.2  7.26
    ## 10           10  51.5  8.24
    ## 11           11  51.5  9.58
    ## 12           12  48.1 11.5

``` r
data %>%
  describeBy(group = "classroom_id")
```

    ## 
    ##  Descriptive statistics by group 
    ## classroom_id: 1
    ##              vars  n  mean   sd median trimmed  mad   min   max range  skew
    ## math_score      1 20 54.75 8.58  54.74   55.41 8.09 35.91 67.60 31.69 -0.60
    ## classroom_id    2 20  1.00 0.00   1.00    1.00 0.00  1.00  1.00  0.00   NaN
    ## student_id      3 20 10.50 5.92  10.50   10.50 7.41  1.00 20.00 19.00  0.00
    ## ses             4 20 54.70 7.53  54.22   55.07 8.45 39.52 64.79 25.27 -0.19
    ##              kurtosis   se
    ## math_score      -0.46 1.92
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -1.17 1.68
    ## ------------------------------------------------------------ 
    ## classroom_id: 2
    ##              vars  n  mean    sd median trimmed   mad   min   max range  skew
    ## math_score      1 20 41.84 10.41  43.45   42.01 10.59 20.82 61.77 40.94 -0.15
    ## classroom_id    2 20  2.00  0.00   2.00    2.00  0.00  2.00  2.00  0.00   NaN
    ## student_id      3 20 10.50  5.92  10.50   10.50  7.41  1.00 20.00 19.00  0.00
    ## ses             4 20 54.47 14.42  49.96   54.82 13.74 23.27 76.94 53.68 -0.11
    ##              kurtosis   se
    ## math_score      -0.80 2.33
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -1.01 3.23
    ## ------------------------------------------------------------ 
    ## classroom_id: 3
    ##              vars  n  mean    sd median trimmed   mad   min   max range  skew
    ## math_score      1 20 46.10 10.23  44.02   45.68 10.38 30.16 64.99 34.83  0.34
    ## classroom_id    2 20  3.00  0.00   3.00    3.00  0.00  3.00  3.00  0.00   NaN
    ## student_id      3 20 10.50  5.92  10.50   10.50  7.41  1.00 20.00 19.00  0.00
    ## ses             4 20 57.27 11.05  60.35   58.07 11.46 34.90 73.12 38.22 -0.44
    ##              kurtosis   se
    ## math_score      -1.18 2.29
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -0.96 2.47
    ## ------------------------------------------------------------ 
    ## classroom_id: 4
    ##              vars  n  mean    sd median trimmed   mad   min   max range skew
    ## math_score      1 20 53.15  8.70  52.86   52.90  7.78 36.54 73.12 36.58 0.34
    ## classroom_id    2 20  4.00  0.00   4.00    4.00  0.00  4.00  4.00  0.00  NaN
    ## student_id      3 20 10.50  5.92  10.50   10.50  7.41  1.00 20.00 19.00 0.00
    ## ses             4 20 47.57 13.25  48.13   46.92 12.64 24.07 73.70 49.63 0.21
    ##              kurtosis   se
    ## math_score      -0.26 1.94
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -0.84 2.96
    ## ------------------------------------------------------------ 
    ## classroom_id: 5
    ##              vars  n  mean    sd median trimmed   mad   min   max range skew
    ## math_score      1 20 46.44 13.61  43.68   45.86 15.82 24.08 71.83 47.75 0.25
    ## classroom_id    2 20  5.00  0.00   5.00    5.00  0.00  5.00  5.00  0.00  NaN
    ## student_id      3 20 10.50  5.92  10.50   10.50  7.41  1.00 20.00 19.00 0.00
    ## ses             4 20 62.54 10.34  61.80   62.21 11.97 47.19 86.27 39.08 0.35
    ##              kurtosis   se
    ## math_score      -0.99 3.04
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -0.82 2.31
    ## ------------------------------------------------------------ 
    ## classroom_id: 6
    ##              vars  n  mean    sd median trimmed   mad   min   max range skew
    ## math_score      1 20 47.17 13.55  46.24   46.79 14.99 25.61 74.32 48.71 0.18
    ## classroom_id    2 20  6.00  0.00   6.00    6.00  0.00  6.00  6.00  0.00  NaN
    ## student_id      3 20 10.50  5.92  10.50   10.50  7.41  1.00 20.00 19.00 0.00
    ## ses             4 20 53.56 12.01  52.85   53.53  9.33 31.63 77.36 45.72 0.07
    ##              kurtosis   se
    ## math_score      -0.97 3.03
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -0.64 2.69
    ## ------------------------------------------------------------ 
    ## classroom_id: 7
    ##              vars  n  mean   sd median trimmed  mad   min   max range  skew
    ## math_score      1 20 57.13 6.75  57.43   56.56 6.86 47.15 72.50 25.35  0.52
    ## classroom_id    2 20  7.00 0.00   7.00    7.00 0.00  7.00  7.00  0.00   NaN
    ## student_id      3 20 10.50 5.92  10.50   10.50 7.41  1.00 20.00 19.00  0.00
    ## ses             4 20 62.10 5.62  61.00   62.27 5.60 51.35 72.38 21.03 -0.10
    ##              kurtosis   se
    ## math_score      -0.43 1.51
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -0.74 1.26
    ## ------------------------------------------------------------ 
    ## classroom_id: 8
    ##              vars  n  mean   sd median trimmed   mad   min   max range  skew
    ## math_score      1 20 44.01 7.56  41.29   42.97  6.50 34.46 66.56 32.10  1.32
    ## classroom_id    2 20  8.00 0.00   8.00    8.00  0.00  8.00  8.00  0.00   NaN
    ## student_id      3 20 10.50 5.92  10.50   10.50  7.41  1.00 20.00 19.00  0.00
    ## ses             4 20 62.87 9.87  62.45   63.10 12.56 45.70 77.47 31.78 -0.09
    ##              kurtosis   se
    ## math_score       1.70 1.69
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -1.31 2.21
    ## ------------------------------------------------------------ 
    ## classroom_id: 9
    ##              vars  n  mean   sd median trimmed  mad   min   max range  skew
    ## math_score      1 20 51.25 7.26   49.9   50.31 6.63 41.44 70.66 29.22  1.00
    ## classroom_id    2 20  9.00 0.00    9.0    9.00 0.00  9.00  9.00  0.00   NaN
    ## student_id      3 20 10.50 5.92   10.5   10.50 7.41  1.00 20.00 19.00  0.00
    ## ses             4 20 62.47 7.83   61.5   62.60 8.84 46.39 75.23 28.84 -0.06
    ##              kurtosis   se
    ## math_score       0.45 1.62
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -0.97 1.75
    ## ------------------------------------------------------------ 
    ## classroom_id: 10
    ##              vars  n  mean   sd median trimmed  mad   min   max range  skew
    ## math_score      1 20 51.52 8.24  50.92   51.42 7.06 37.26 71.09 33.83  0.37
    ## classroom_id    2 20 10.00 0.00  10.00   10.00 0.00 10.00 10.00  0.00   NaN
    ## student_id      3 20 10.50 5.92  10.50   10.50 7.41  1.00 20.00 19.00  0.00
    ## ses             4 20 51.69 5.41  52.16   51.91 4.35 38.59 61.66 23.07 -0.37
    ##              kurtosis   se
    ## math_score      -0.19 1.84
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -0.13 1.21
    ## ------------------------------------------------------------ 
    ## classroom_id: 11
    ##              vars  n  mean    sd median trimmed   mad   min   max range  skew
    ## math_score      1 20 51.50  9.58  51.06   51.23 10.97 35.73 68.03 32.30  0.08
    ## classroom_id    2 20 11.00  0.00  11.00   11.00  0.00 11.00 11.00  0.00   NaN
    ## student_id      3 20 10.50  5.92  10.50   10.50  7.41  1.00 20.00 19.00  0.00
    ## ses             4 20 65.36 11.68  65.81   65.41 14.86 44.58 83.95 39.37 -0.05
    ##              kurtosis   se
    ## math_score      -1.24 2.14
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -1.41 2.61
    ## ------------------------------------------------------------ 
    ## classroom_id: 12
    ##              vars  n  mean    sd median trimmed   mad   min   max range skew
    ## math_score      1 20 48.11 11.47  44.57   47.21 13.25 35.30 70.65 35.34 0.41
    ## classroom_id    2 20 12.00  0.00  12.00   12.00  0.00 12.00 12.00  0.00  NaN
    ## student_id      3 20 10.50  5.92  10.50   10.50  7.41  1.00 20.00 19.00 0.00
    ## ses             4 20 59.80  3.43  59.55   59.76  3.68 53.63 66.44 12.81 0.15
    ##              kurtosis   se
    ## math_score      -1.34 2.56
    ## classroom_id      NaN 0.00
    ## student_id      -1.38 1.32
    ## ses             -1.03 0.77

Great! It looks like we have 10 students per classroom and their mean
math scores range between 45 and 55. Also, thanks to the `psych`
package, we get detailed group-level descriptive statistics for each
classroom.

Let’s visualize this information using a histogram. To do this, we can
use the `ggplot2` package. This package allows us to create beautiful
and informative data visualizations. In the code block below, we will
use the `ggplot2` package to create a histogram of our math scores.
Also, let’s bring in the `facet_wrap` function to create a histogram for
each classroom.

``` r
# Load ggplot2 package
library(ggplot2)

# Plot histogram
ggplot(data, aes(x = math_score)) +
  geom_histogram(binwidth = 5) +
  facet_wrap(~classroom_id)
```

![](index_files/figure-markdown_github/unnamed-chunk-4-1.png)

This histogram shows us the distribution of math scores across all
classrooms. For the most part, all classroom scores appear to be
normally distributed.

## Centering our Data

According to best practices, we want to mean and grand mean center our
data before running our mixed effects model. This is because centering
our data allows us to interpret the intercept as the mean of the outcome
variable when all predictors are at their mean. In the code block below,
we will use the `MLMusingR` and `misty` package to mean and grand mean
center our data.

``` r
library(MLMusingR) #using for centering
library(misty) #used for grand mean centering

# Creating Group Means for SES
data <- data %>%
  group_by(classroom_id) %>%
  mutate(ses_grpmean = mean(ses)) %>%
  ungroup()

# Group mean centering SES
data <- data %>%
  mutate(ses_mc = group_center(ses, classroom_id))

data$ses_mc <- as.numeric(data$ses_mc)

# Grand mean centering Gender
data <- data %>%
  mutate(ses_gmc = center(ses_grpmean, type = "CGM", cluster = classroom_id))
```

When mean centering our data within the context of HLM, our mean
centered variable is the difference between the individual score and the
group mean. For example, if a student’s SES score is 60 and the mean SES
score for their classroom is 50, then the mean centered SES score for
that student is 10.

When grand mean centering our data within the context of HLM, our grand
mean centered variable is the difference between the group mean and the
grand mean. For example, if the mean SES score for a classroom is 50 and
the grand mean SES score for the entire sample is 45, then the grand
mean centered SES score for that classroom is 5.

Here’s how your dataframe should look like up to this point:

``` r
head(data)
```

    ## # A tibble: 6 × 7
    ##   math_score classroom_id student_id   ses ses_grpmean ses_mc ses_gmc
    ##        <dbl>        <int>      <int> <dbl>       <dbl>  <dbl>   <dbl>
    ## 1       56.0            1          1  59.0        54.7   4.32   -3.17
    ## 2       38.9            1          2  59.4        54.7   4.70   -3.17
    ## 3       53.3            1          3  53.3        54.7  -1.37   -3.17
    ## 4       67.6            1          4  63.9        54.7   9.18   -3.17
    ## 5       35.9            1          5  51.2        54.7  -3.52   -3.17
    ## 6       52.8            1          6  63.4        54.7   8.66   -3.17

## Running our Model

Now that we have explored our data and centered our variables, we are
ready to run our HLM model. In the code block below, we will use the
`lme4` package to run our models. The `lme4` package is a powerful tool
that allows us to run mixed effects models and has a similar syntax to
the `lm` function. The `lmerTest` package is an extension of the `lme4`
package and allows us to run significance tests for our models.

### Null Model

``` r
# Load lme4 package
library(lme4)
library(lmerTest)

# Run null model
null_model <- lmer(math_score ~ 1 + (1 | classroom_id), data = data)
summary(null_model)
```

    ## Linear mixed model fit by REML. t-tests use Satterthwaite's method [
    ## lmerModLmerTest]
    ## Formula: math_score ~ 1 + (1 | classroom_id)
    ##    Data: data
    ## 
    ## REML criterion at convergence: 1795.7
    ## 
    ## Scaled residuals: 
    ##      Min       1Q   Median       3Q      Max 
    ## -2.32776 -0.68944 -0.01453  0.71440  2.68700 
    ## 
    ## Random effects:
    ##  Groups       Name        Variance Std.Dev.
    ##  classroom_id (Intercept) 15.75    3.968   
    ##  Residual                 98.15    9.907   
    ## Number of obs: 240, groups:  classroom_id, 12
    ## 
    ## Fixed effects:
    ##             Estimate Std. Error     df t value Pr(>|t|)    
    ## (Intercept)   49.412      1.312 11.000   37.66 5.58e-13 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

In this model, we are predicting math scores using a random intercept
for classroom. The random intercept for classroom is represented by
`(1 | classroom_id)`. The `1` represents the intercept and the
`classroom_id` represents the groups.

### Full Model

Now, let’s run our full model. We want to see if SES has an effect on
student math scores. We are also adding an interaction effect between
student level SES and classroom level SES to investigate how the
classroom makeup may impact student performance.

``` r
# Run full model
full_model <- lmer(math_score ~  ses_mc + ses_gmc + ses_mc:ses_gmc + (1 + ses_mc | classroom_id), data = data)
summary(full_model)
```

    ## Linear mixed model fit by REML. t-tests use Satterthwaite's method [
    ## lmerModLmerTest]
    ## Formula: math_score ~ ses_mc + ses_gmc + ses_mc:ses_gmc + (1 + ses_mc |  
    ##     classroom_id)
    ##    Data: data
    ## 
    ## REML criterion at convergence: 1794.6
    ## 
    ## Scaled residuals: 
    ##      Min       1Q   Median       3Q      Max 
    ## -2.51075 -0.71516 -0.03546  0.56583  2.81292 
    ## 
    ## Random effects:
    ##  Groups       Name        Variance  Std.Dev. Corr
    ##  classroom_id (Intercept) 1.795e+01 4.23623      
    ##               ses_mc      4.758e-04 0.02181  1.00
    ##  Residual                 9.362e+01 9.67589      
    ## Number of obs: 240, groups:  classroom_id, 12
    ## 
    ## Fixed effects:
    ##                 Estimate Std. Error        df t value Pr(>|t|)    
    ## (Intercept)     49.41236    1.37316  10.00212  35.984  6.5e-12 ***
    ## ses_mc           0.23741    0.06580 151.34858   3.608 0.000418 ***
    ## ses_gmc         -0.05353    0.26161  10.00212  -0.205 0.841981    
    ## ses_mc:ses_gmc   0.01337    0.01135 132.69882   1.178 0.240735    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Correlation of Fixed Effects:
    ##             (Intr) ses_mc ss_gmc
    ## ses_mc      0.085               
    ## ses_gmc     0.000  0.000        
    ## ss_mc:ss_gm 0.000  0.162  0.094 
    ## optimizer (nloptwrap) convergence code: 0 (OK)
    ## boundary (singular) fit: see help('isSingular')

In this model, we are predicting math scores using ses_mc, ses_gmc, and
the interaction between these two variables. We are also allowing the
intercept and ses_mc to vary across classrooms. The random intercept and
ses_mc for classroom is represented by `(1 + ses_mc | classroom_id)`.

The summary output provides estimates for the fixed and random effects
produced by the model.

## Summary

In conclusion, this tutorial provides an introduction to mixed effects
models (HLM) and explains how they can be used to analyze educational
research data that exhibit a nested or hierarchical structure. By taking
into account the nested structure of the data and allowing for the
estimation of both fixed and random effects, mixed effects models can
improve the accuracy of the estimates, increase statistical power, and
provide a more comprehensive understanding of the underlying processes.
The tutorial uses the R programming language and the `lme4` package to
illustrate the basics of mixed effects modeling. It also provides
step-by-step instructions exploring the data using descriptive
statistics. Future tutorials include the use of mixed effects/HLM
approaches using longitiduinal data.
