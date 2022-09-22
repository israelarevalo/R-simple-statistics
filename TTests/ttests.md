Conducting T-Tests in R - A Friendly Introduction
================
Israel Arevalo
9/22/22

-   <a href="#before-we-start" id="toc-before-we-start">Before we start</a>
-   <a href="#t-tests---what-are-they-and-how-can-we-use-them"
    id="toc-t-tests---what-are-they-and-how-can-we-use-them">T-Tests - What
    are they and how can we use them?</a>

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
will get you up to speed \[insert link to R installation here\]. 1. You
have installed R and an IDE such as RStudio on your computer 2. You have
a dataset to work with (preferably in .csv format)

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
teachers that are implementing two different reading interventions in
their classroom. After a 6-week implementation period, the teachers want
to know whether there was a significant difference in the students’
performance between each class. In other words, did the students in
Classroom A (who received Intervention A) differ in mean reading
performance scores compared to Classroom B (who received Intervention
B)? Assuming all students started at the exact same average performance
in reading, and only one measure was used to determine student reading
performance scores (post-intervention), we can use a T-Test to compare
whether there is a significant difference in the mean scores between the
students in Classroom A and Classroom B.

Us
