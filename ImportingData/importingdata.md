Importing Data into R
================
Israel Arevalo
2022-09-23

-   <a href="#introduction" id="toc-introduction">Introduction</a>
-   <a href="#importing-from-a-mac-environment"
    id="toc-importing-from-a-mac-environment">Importing from a Mac
    environment</a>
-   <a href="#importing-from-a-windows-environment"
    id="toc-importing-from-a-windows-environment">Importing from a Windows
    environment</a>

## Introduction

This guide was created to assist a new user of R in importing your
dataset. R is a powerful tool that allows the user to import a variety
of data types (i.e., .csv, .sas, .dat, and more).

<div class="callout-note" appearance="simple">

Some data types will require for you to take an additional step before
you can successfully import your data.

</div>

Specifically, these data types include:

-   data 1
-   data 2
-   data 3

For this reason, I recommend you start with a .csv file if you are able
to. If you have an alternative data type specific to a particular
statistical package (i.e., SPSS), you can export your dataset into a
.csv format; however, the steps to that procedure are beyond the scope
of this article.

If you are unable to initially import your data as a .csv format, scroll
to the bottom

## Importing from a Mac environment

To begin our tutorial, we will discuss importing your dataset from
within a Mac environment. Several assumptions are made prior to this
process.

1.  You have installed R on your computer.
2.  You have installed an Integrated Development Environment (IDE) like
    Rstudio on your computer.
3.  You have a dataset that you can import into RStudio.

``` r
# Importing dataset from existing file and calling it 'df'

#df <- read.csv('dataset.csv')
```

# Importing from a Windows environment

Importing your data within a Windows environment is very similar to what
we have dissed up to this point. Also, we will be making the same
assumptions we have previously discussed (installed R, installed
RStudio, and have a dataset).