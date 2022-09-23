Importing Data into R
================
Israel Arevalo
2022-09-23

-   <a href="#getting-started" id="toc-getting-started">Getting Started</a>
-   <a href="#importing-from-a-mac-environment"
    id="toc-importing-from-a-mac-environment">Importing from a Mac
    environment</a>
-   <a href="#importing-from-a-windows-environment"
    id="toc-importing-from-a-windows-environment">Importing from a Windows
    environment</a>

## Getting Started

This guide was created to assist a new user of R in importing your
dataset. R is a powerful tool that allows the user to import a variety
of data types (i.e., .csv, .sas, .dat, and more). To start, we want to
download a package called `haven` by running the following code.

> **Note**   
> Before copying/pasting into your IDE (i.e., RStudio), please make sure
> to not copy the `#` as it will make a note instead of run the
> `install.packages` function.

    install.packages('haven')

Now that we’ve downloaded the `haven` package, let’s load it into our
environment. We will do that by running the `library` function. Notice
that now that we have installed our library, we don’t need to wrap our
library’s name with ” ” or ’ ’. However, doing so will not yield an
error.

    library(haven)

The Haven library will allow you to import a variety of data types into
your R environment. In order to `read` these datasets, we will be using
the `read_DATATYPE` function. Let’s take a look at that now

Some data types will require for you to take an additional step before
you can successfully import your data.

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

<!-- -->


    # Importing dataset from existing file and calling it 'df'

    df <- read.csv('dataset.csv')

# Importing from a Windows environment

Importing your data within a Windows environment is very similar to what
we have dissed up to this point. Also, we will be making the same
assumptions we have previously discussed (installed R, installed
RStudio, and have a dataset).
