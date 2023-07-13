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
-   <a href="#exploring-our-data" id="toc-exploring-our-data">Exploring our
    Data</a>
-   <a href="#conducting-t-test" id="toc-conducting-t-test">Conducting
    T-Test</a>
-   <a href="#interpreting-the-results"
    id="toc-interpreting-the-results">Interpreting the Results</a>
-   <a href="#conclusion" id="toc-conclusion">Conclusion</a>

## Before we start

This guide was designed as a friendly approach to learn how to conduct
statistical analysis within the R environment. R is a powerful
open-source tool that allows for great flexibility and customization of
when conducting data wrangling (cleaning), analysis, interpretation, and
reporting. Did I mention its open-source? This not only means that it is
free, but the entire code used to develop this exceptional language is
entirely available for you to view (and even suggest changes) online.

Several assumptions about the user will be made within this guide.

1.  You have installed R and an IDE such as RStudio on your computer
2.  You have a dataset to work with (preferably in .csv format)

If these assumptions are not met, here are a few guides to reference
that will get you up to speed

-   [Installing
    RStudio](https://rstudio-education.github.io/hopr/starting.html)
-   [Exporting SPSS dataset to
    .csv](https://www.ibm.com/docs/en/spss-statistics/beta?topic=files-exporting-datasets)
-   [Exporting STATA dataset to
    .csv](https://stats.oarc.ucla.edu/stata/faq/how-do-i-export-stata-dta-files-to-comma-separated-files/)
-   [Exporting Excel file to
    .csv](https://support.microsoft.com/en-us/office/import-or-export-text-txt-or-csv-files-5250ac4c-663c-47ce-937b-339e391393ba)

## T-Tests - What are they and how can we use them?

T-Tests are among one of the most popular, albeit basic, statistical
analysis out there. In a nutshell, T-Tests provide an insight into
**mean group differences** of two variables. A limitation within the
T-Test is that it only allows the comparison of **two** groups -
anything larger than two would require approaches such as an ANOVA
(discussed in other guides). This guide is far from a lecture on T-Tests
and their use. If you would like to learn more about T-Tests here is a
useful guide [T-Test
Introduction](https://www.jmp.com/en_us/statistics-knowledge-portal/t-test.html).
Below is a short scenario to provide you context for the remainder of
the tutorial.

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
# A seed allows us to pull in the same randomly generated numbers each time we run this command. If we don't set a seed, we will generate different numbers each time we run this command
set.seed(123) 

# This command randomly generates a sample of 25 scores between 60-100 for each intervention group (a, b) 
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

## Exploring our Data

Let’s take a look at simple descriptive statisitcs within our data.
Running the base (summary) command allows us to look at information such
as the minimum, maximum, quartiles, median, and means for each variable
within our dataset. You can further customize this command by adding
further adjustments to the command itself. For example, if we want to
run a summary command on just 1 or 2 variables within a dataset
containing many other variables, we can adjust our code to specify which
variables we are interested in by adding a `$` symbol after the name of
the dataset and then specifying the name of the variable (i.e.,
`summary(df$intervention_a`). Below we have run a simple `summary`
command to explore our generated dataset. Since we only generated two
variables, we do not need to specify which variables we are interested
in.

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

Based on our summary output, we can see that students that received the
reading intervention A have a higher mean average score on their
post-intervention reading task when compared to those students that
received reading intervention b.

To determine whether this difference is statistically significant, we
will proceed in conducing our T-Test.

## Conducting T-Test

R allows us to run our analysis with only one line of code. Below is a
code block that demonstrates the code that we used to run our T-Test.
The *function* that we use to call the T-Test formula is called
`t.test`. After this function we have an open/close parenthesis. Within
these parenthesis, we place our two groups `intervention_a` and
`intervention_b`.

That’s it! We have successfully conducted a simple T-Test using our
variables of interest. In the next section, we will be interpreting the
output that our `t.test` function generated.

``` r
t.test(intervention_a, intervention_b)
```


        Welch Two Sample t-test

    data:  intervention_a and intervention_b
    t = 0.45657, df = 47.731, p-value = 0.6501
    alternative hypothesis: true difference in means is not equal to 0
    95 percent confidence interval:
     -5.583308  8.863308
    sample estimates:
    mean of x mean of y 
        81.48     79.84 

## Interpreting the Results

When we conduct a hypothesis test, we want to establish our Null
Hypothesis and our Alternative Hypothesis. When conducting T-Tests, our
Null Hypothesis (H0) is often reported as H0 = Mu1 = Mu2 - in other
words, the mean of group 1 are equal to the mean of group 2 (in its most
simplist form). Alternatively (no pun intended), our alternative
hypothesis (H1) states that H1 = Mu1 != Mu2 - in other words, the mean
of group 1 is not equal to the mean of group 2.

To test whether we reject or fail to reject our null hypothesis, we take
a look at our T-Test results above.

Now, let’s take a look at our T-Test output. We can see that we have a
`t-value` of `0.457`, `degrees of freedom` of `47.731` and a significant
value `(p-value)` of `0.65`. Based on these results, we would ***fail to
reject*** our null hypothesis and conclude that based on our data,
studentss’ scores did not vary by a statistically significant amount
regardless of which reading intervention they received. In other words,
the mean differences between `intervention_a` and `intervention_b` did
not vary by a statistically significant amount.

## Conclusion

If you followed along, you can see that running a simple T-Test within
the R environment is something that can be done quickly once your data
is cleaned and organized. R provides the flexibility to manipulate and
transform your data in an efficient and reproducible way. If you haven’t
already, try copying and pasting these codes into your R environment and
play with the variables. Also, consider saving your script/rmarkdown
file for later use as this may significantly increase your research
workflow.
