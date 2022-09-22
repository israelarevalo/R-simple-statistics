Conducting T-Tests in R - A Friendly Introduction
================
Israel Arevalo
9/22/22

-   <a href="#before-we-start" id="toc-before-we-start">Before we start</a>
-   <a href="#t-tests---what-are-they-and-how-can-we-use-them"
    id="toc-t-tests---what-are-they-and-how-can-we-use-them">T-Tests - What
    are they and how can we use them?</a>
-   <a href="#generating-our-dataset"
    id="toc-generating-our-dataset">Generating our Dataset</a>
-   <a href="#exploratory-data-analysis"
    id="toc-exploratory-data-analysis">Exploratory Data Analysis</a>

## Before we start

This guide was designed as a friendly approach to learn how to conduct
statistical analysis within the R environment. R is a powerful
open-source tool that allows for great flexibility and customization of
when conducting data wrangling (cleaning), analysis, interpretation, and
reporting. Did I mention its open-source? This not only means that it is
free, but the entire code used to develop this exceptional language is
entirely available for you to view (and even suggest changes) online.

Several assumptions about the user will be made within this guide. If
these assumptions are not met, here are a few guides to reference that
will get you up to speed \[insert link to R installation here\].

1.  You have installed R and an IDE such as RStudio on your computer
2.  You have a dataset to work with (preferably in .csv format)

## T-Tests - What are they and how can we use them?

T-Tests are among one of the most popular, albeit basic, statistical
analysis out there. In a nutshell, T-Tests provide an insight into
**mean group differences** of two variables. A limitation within the
T-Test is that it only allows the comparison of **two** groups -
anything larger than two would require approaches such as an ANOVA
(discussed in other guides). This guide is far from a lecture on T-Tests
and their use. If you would like to learn more about T-Tests here is a
useful guide \[insert link here\]. Below is a short scenario to provide
you context for the remainder of the tutorial.

As a brief example of a T-Test, let’s say you have two classroom
teachers that are implementing different reading interventions in their
classrooms (Intervention A and Intervention B). After a 6-week
implementation period, the teachers want to know whether there was a
significant difference in the students’ performance between each class.
In other words, did the students in Classroom A (who received
Intervention A) differ in mean reading performance scores compared to
Classroom B (who received Intervention B)? Assuming all students started
at the exact same average performance in reading, and only one measure
was used to determine student reading performance scores
(post-intervention), we can use a T-Test to compare whether there is a
significant difference in the mean scores between the students in
Classroom A and Classroom B.

> **Note**
>
> The above scenario is fictitious and may not be the best way to
> approach the investigation of an intervention’s efficacy. This
> scenario is only to give you context for the code we will discuss
> below.

## Generating our Dataset

Using randomly-generated data, let’s build a fictitious dataset to math
the scenario above and that we will use to run our T-Test.

> **Warning**
>
> This step is only required for the instructional nature of this
> tutorial. You will **not** need to run this step as you do not need to
> randomly generate your data.

In the code block below, we will generate an array consisting of test
scores from students (rows) who received intervention A and B (columns).
Since we are taking quite the creative freedoms here, let’s also assume
that all students score at least a 60 or above with the max possible
score of 100. Let’s also assume that we had an equal amount of students
in each classroom (n = 25).

``` r
set.seed(123)
intervention_a <- sample(60:100, size = 25)
intervention_b <- sample(60:100, size = 25)

#making the dataframe (combining the two variables into one array) - we will call it "df"
df = (rbind(intervention_a, intervention_b))
#Transpose Dataframe
df <- t(df) 

#Display first 6 rows in our data
head(df)
```

         intervention_a intervention_b
    [1,]             90             98
    [2,]             74             71
    [3,]             73             74
    [4,]             62             91
    [5,]             96             66
    [6,]             98             68

## Exploratory Data Analysis

``` r
library(tidyverse)
```

    ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
    ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()

``` r
summary(df)
```

     intervention_a  intervention_b  
     Min.   :62.00   Min.   : 60.00  
     1st Qu.:69.00   1st Qu.: 68.00  
     Median :85.00   Median : 80.00  
     Mean   :81.48   Mean   : 79.84  
     3rd Qu.:92.00   3rd Qu.: 91.00  
     Max.   :98.00   Max.   :100.00  

``` r
hist(intervention_a)
hist(intervention_b)
```

<img
src="ttests.markdown_github_files/figure-markdown_github/figures-side-1.png"
style="width:50.0%" />

<img
src="ttests.markdown_github_files/figure-markdown_github/figures-side-2.png"
style="width:50.0%" />