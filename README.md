
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Rix: Reproducible Environments with Nix

## Introduction

<!-- badges: start -->

[![R-CMD-check](https://github.com/b-rodrigues/rix/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/b-rodrigues/rix/actions/workflows/R-CMD-check.yaml)
<!-- badges: end -->

**WARNING: This package is in very early development. I’ve literally
have only worked 4 hours on it. DO NOT USE IT\!**

`{rix}` is an R package that provides helper functions to help you setup
development environments that contain all the required packages that you
need for your project. This is achieved by using the Nix package manager
that you must install separately. The Nix package manager is extremely
powerful: with it, it is possible to work on totally reproducible
development environments, and even install old releases of R and R
packages. With Nix, it is essentially possible to replace `{renv}` and
Docker combined. Nix is available for Linux, macOS and Windows.

## The Nix package manager

Nix is a piece of software that can be installed on your computer
(regardless of OS) and can be used to install software like with any
other package manager. If you’re familiar with the Ubuntu Linux
distribution, you likely have used `apt-get` to install software. On
macOS, you may have used `homebrew` for similar purposes. Nix functions
in a similar way, but has many advantages over classic package managers.
The main advantage of Nix, at least for our purposes, is that its
repository of software is huge. As of writing, it contains more than
80’000 packages, and the entirety of CRAN is available through Nix’s
repositories. This means that using Nix, it is possible to install not
only R, but also all the packages required for your project. The obvious
question is why use Nix instead of simply installing R and R packages as
usual. The answer is that Nix makes sure to install every dependency of
any package, up to required system libraries. For example, the `{xlsx}`
package requires the Java programming language to be installed on your
computer to successfully install. This can be difficult to achieve, and
`{xlsx}` bullied many R developers throughout the years (especially
those using a Linux distribution, `sudo R CMD javareconf` still plagues
my nightmares). But with Nix, it suffices to declare that we want the
`{xlsx}` package for our project, and Nix figures out automatically that
Java is required and installs and configures it. It all just happens
without any required intervention from the user. The second advantage of
Nix is that it is possible to *pin* a certain *revision* for our
project. Pinning a revision ensures that every package that Nix installs
will always be at exactly the same versions, regardless of when in the
future the packages get installed.

## Rix workflow

The idea of `{rix}` is for you to declare the environment you need, and
then continue working on that isolated environment. It is possible to
have as many environments as projects. Each environment is isolated (or
not, it’s up to you).

The main function of `{rix}` is called `rix()`. `rix()` has 4 arguments:

  - the R version you need for your project
  - a list of R packages that your project needs
  - whether you want to use RStudio as an IDE for your project
  - a path to save a file called `default.nix`.

### default.nix

The Nix package manager can be used to build reproducible development
environments according to the specifications found in a file called
`default.nix`. To make it easier for R programmers to use Nix, `{rix}`
can be used to write this file for you. Once this file has been written,
go to where you chose to write it (ideally in a new, empty folder that
will be the root folder of your project) and use the Nix package manager
to build the environment. Call the following function in a terminal:

    nix-build

Once Nix done building the environment, you can start working on it
interactively by using the following command:

    nix-shell

You will *drop* in a Nix shell. You can now call the IDE of your choice.
For RStudio, simply call:

    rstudio

This will start RStudio. RStudio will use the version of R and library
of packages from that environment.

### Running programs from an environment

You could create a bash script that you put in the path to make this
process more streamlined. For example, if your project is called
`housing`, you could create this script and execute it to start your
project:

    !#/bin/bash
    nix-shell /absolute/path/to/housing/default.nix --run rstudio

This will execute RStudio in the environment for the `housing` project.
If you use `{targets}` you could execute the pipeline in the environment
by running:

    nix-shell /absolute/path/to/housing/default.nix --run Rscript -e 'targets::tar_make()'

## Installation

You can install the development version of rix from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("b-rodrigues/rix")
```

## Example

This is a basic example which shows you how to solve a common problem:

``` r
library(rix)
## basic example code
```

What is special about using `README.Rmd` instead of just `README.md`?
You can include R chunks like so:

``` r
summary(cars)
#>      speed           dist       
#>  Min.   : 4.0   Min.   :  2.00  
#>  1st Qu.:12.0   1st Qu.: 26.00  
#>  Median :15.0   Median : 36.00  
#>  Mean   :15.4   Mean   : 42.98  
#>  3rd Qu.:19.0   3rd Qu.: 56.00  
#>  Max.   :25.0   Max.   :120.00
```

You’ll still need to render `README.Rmd` regularly, to keep `README.md`
up-to-date. `devtools::build_readme()` is handy for this.

You can also embed plots, for example:

<img src="man/figures/README-pressure-1.png" width="100%" />

In that case, don’t forget to commit and push the resulting figure
files, so they display on GitHub and CRAN.
