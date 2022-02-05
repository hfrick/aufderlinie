---
output: hugodown::hugo_document

title: goodpractice 1.0.2 on CRAN
author: Hannah Frick
date: '2018-05-11'
slug: goodpractice-on-cran
categories:
  - R
tags:
  - goodpractice
  - pkg-building
comments: no
---


This post first appeared on the [Mango blog](https://www.mango-solutions.com/blog/goodpractice-1-0-2-on-cran).

---

We are excited to annouce that the **goodpractice** package is now available on [CRAN](https://CRAN.R-project.org/package=goodpractice). The package gives advice about good practices when building R packages. Advice includes functions and syntax to avoid, package structure, code complexity, code formatting, etc.

Install the CRAN version via

```
install.packages("goodpractice")
```

## Building R packages

Building an R package is a great way of encapsulating code, documentation and data in a single testable and easily distributable unit.

For a package to be distributed via CRAN, it needs to pass a set of checks implemented in `R CMD check`, such as: Is there minimal documentation, e.g., are all arguments of exported functions documented? Are all dependencies declared?

These checks are helpful in developing a solid R package but they don't check for several other good practices. For example, a package does not need to contain any tests but is it good practice to include some. Following a coding standard helps readability. Avoiding overly complex functions reduces the risk of bugs. Including an URL for bug reports lets people more easily report bugs if they find any. 

### What the **goodpractice** package does

Tools for automatically checking several of the above mentioned aspects already exist and the **goodpractice** package bundles the checks from [**rcmdcheck**](https://cran.r-project.org/package=rcmdcheck) with code coverage through the [**covr**](https://cran.r-project.org/package=covr) package, source code linting via the [**lintr**](https://cran.r-project.org/package=lintr) package and cyclompatic complexity via the [**cyclocomp**](https://cran.r-project.org/package=cyclocomp) package and augments it with some further checks on good practice for R package development such as avoiding `T` and `F` in favour of `TRUE` and `FALSE`. It provides advice on which practices to follow and which to avoid.

You can use **goodpractice** checks as a reminder for you and your collegues - and if you have custom checks to run, you can make **goodpractice** run those as well! 

## How to use **goodpractice**

The main fuction `goodpractice()` (and its alias `gp()`) takes the path to the source code of a package as its first argument. The **goodpractice** package contains the source for a simple package which violates some good practices. We'll use this for the examples.


```r
library(goodpractice)

# get path to example package
pkg_path <- system.file("bad1", package = "goodpractice")

# run gp() on it
g <- gp(pkg_path)
```

```
## 
## * checking for file ‘/private/var/folders/f3/gr9my1s97r3654cmvzc6dngm0000gn/T/RtmpYGQL9R/remotes4049302963ce/badpackage/DESCRIPTION’ ... OK
## * preparing ‘badpackage’:
## * checking DESCRIPTION meta-information ... OK
## * checking vignette meta-information ... OK
## * checking for LF line-endings in source and make files and shell scripts
## * checking for empty or unneeded directories
## * building ‘badpackage_1.0.0.tar.gz’
```

```r
# show the result
g
```

```
## ── GP badpackage ───────────────────────────────────────────────────────────────
## 
## It is good practice to
## 
##   ✖ not use "Depends" in DESCRIPTION, as it can cause name clashes, and
##     poor interaction with other packages. Use "Imports" instead.
##   ✖ omit "Date" in DESCRIPTION. It is not required and it gets invalid
##     quite often. A build date will be added to the package when you
##     perform `R CMD build` on it.
##   ✖ add a "URL" field to DESCRIPTION. It helps users find information
##     about your package online. If your package does not have a
##     homepage, add an URL to GitHub, or the CRAN package package page.
##   ✖ add a "BugReports" field to DESCRIPTION, and point it to a bug
##     tracker. Many online code hosting services provide bug trackers for
##     free, https://github.com, https://gitlab.com, etc.
##   ✖ omit trailing semicolons from code lines. They are not needed and
##     most R coding standards forbid them
## 
##     R/semicolons.R:4:30
##     R/semicolons.R:5:29
##     R/semicolons.R:9:38
## 
##   ✖ not import packages as a whole, as this can cause name clashes
##     between the imported packages. Instead, import only the specific
##     functions you need.
##   ✖ fix this R CMD check ERROR: VignetteBuilder package not declared:
##     ‘knitr’ See section ‘The DESCRIPTION file’ in the ‘Writing R
##     Extensions’ manual.
##   ✖ avoid 'T' and 'F', as they are just variables which are set to the
##     logicals 'TRUE' and 'FALSE' by default, but are not reserved words
##     and hence can be overwritten by the user.  Hence, one should always
##     use 'TRUE' and 'FALSE' for the logicals.
## 
##     R/tf.R:NA:NA
##     R/tf.R:NA:NA
##     R/tf.R:NA:NA
##     R/tf.R:NA:NA
##     R/tf.R:NA:NA
##     ... and 4 more lines
## 
## ────────────────────────────────────────────────────────────────────────────────
```

So with this package, we've done a few things in the DESCRIPTION file for which there are reasons not to do them, have unnecessary trailing semicolons in the code and used `T` and `F` for `TRUE` and `FALSE`. The output of `gp()` will tell you what isn’t considered good practice out of what you have already written. If that is in the R code itself, it will also point you to the location of your faux-pas. In general, the messages are supposed to not only point out to you _what_ you might want to avoid but also _why_.


### Custom checks

The above example tries to run all 230 checks available, to see the full list use `all_checks()`. You can customize the set of checks run by selecting only those default checks you are intersted in and by adding your own checks.

If you only want to run a subset of the checks, e.g., just the check on the URL field in the DESCRIPTION, you can specify the checks by name:


```r
# what is the name of the check?
grep("url", all_checks(), value = TRUE)
```

```
## [1] "description_url"
```

```r
# run only this check
gp(pkg_path, checks = "description_url")
```

```
## Preparing: description
```

```
## ── GP badpackage ───────────────────────────────────────────────────────────────
## 
## It is good practice to
## 
##   ✖ add a "URL" field to DESCRIPTION. It helps users find information
##     about your package online. If your package does not have a
##     homepage, add an URL to GitHub, or the CRAN package package page.
## ────────────────────────────────────────────────────────────────────────────────
```

Additional checks can be used in `gp()` via the `extra_checks` argument. This should be a named list of `check` objects as returned by the `make_check()` function.


```r
# make a simple version of the T/F check
check_simple_tf <- make_check(
  
  description = "TRUE and FALSE is used, not T and F",
  gp = "avoid 'T' and 'F', use 'TRUE' and 'FALSE' instead.",
  check = function(state) {
      length(tools::checkTnF(dir = state$path)) == 0
  }
)

gp(pkg_path, checks = c("description_url", "simple_tf"),
   extra_checks = list(simple_tf = check_simple_tf))
```

```
## Preparing: description
```

```
## ── GP badpackage ───────────────────────────────────────────────────────────────
## 
## It is good practice to
## 
##   ✖ add a "URL" field to DESCRIPTION. It helps users find information
##     about your package online. If your package does not have a
##     homepage, add an URL to GitHub, or the CRAN package package page.
##   ✖ avoid 'T' and 'F', use 'TRUE' and 'FALSE' instead.
## ────────────────────────────────────────────────────────────────────────────────
```

For more details on creating custom checks, please see the vignette [Custom Checks](https://mangothecat.github.io/goodpractice/articles/custom_checks.html).

## Acknowledgements

This package was written by [Gábor Csárdi](https://github.com/gaborcsardi) with contributions by 
[Noam Ross](https://github.com/noamross), [Neal Fultz](https://github.com/nfultz), [Douglas Ashton](https://github.com/dougmet), [Marcel Ramos](https://github.com/LiNk-NY), [Joseph Stachelek](https://github.com/jsta), and [myself](https://github.com/hfrick). Special thanks for the input and feedback to the [rOpenSci](https://ropensci.org/) leadership team and community as well as everybody who opened [issues](https://github.com/MangoTheCat/goodpractice/issues)!

## Feedback

If you have any feedback, please consider opening an issue on [GitHub](https://github.com/MangoTheCat/goodpractice/issues).


