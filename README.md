
<!-- README.md is generated from README.Rmd. Please edit that file -->

# grateful: Facilitate citation of R packages

<!-- badges: start -->

[![R-CMD-check](https://github.com/Pakillo/grateful/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/Pakillo/grateful/actions/workflows/R-CMD-check.yaml)
[![Codecov test
coverage](https://codecov.io/gh/Pakillo/grateful/branch/master/graph/badge.svg)](https://app.codecov.io/gh/Pakillo/grateful?branch=master)
[![](https://img.shields.io/badge/lifecycle-stable-brightgreen.svg)](https://lifecycle.r-lib.org/articles/stages.html#stable)
[![Project Status: Active - The project has reached a stable, usable
state and is being actively
developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active)
[![](https://www.r-pkg.org/badges/version/grateful)](https://cran.r-project.org/package=grateful)
[![](https://cranlogs.r-pkg.org/badges/grand-total/grateful)](https://cran.r-project.org/package=grateful)
<!-- badges: end -->

The goal of **grateful** is to make it very easy to cite R and the R
packages used in any analyses, so that package authors receive their
deserved credit. By calling a single function, **grateful** will scan
the project for R packages used and generate a BibTeX file containing
all citations for those packages.

**grateful** can then generate a new document with citations in the
desired output format (Word, PDF, HTML, Markdown). These references can
be formatted for a specific journal, so that we can just paste them
directly into our manuscript or report.

Alternatively, we can use **grateful** directly within an
[Rmarkdown](https://rmarkdown.rstudio.com/) or
[Quarto](https://quarto.org/) document. In this case, a paragraph
containing in-text citations of all used R packages will (optionally) be
inserted into the Rmarkdown/Quarto document, and these packages will be
included in the reference list when rendering.

## Installation

You can install {grateful} from CRAN:

``` r
install.packages("grateful")
```

Or from GitHub:

``` r
# install.packages("remotes")
remotes::install_github("Pakillo/grateful")
```

## Usage

**grateful** can be used in one of two ways:

1.  to generate a ‘citation report’ listing each package and their
    citations

2.  to build citation keys to incorporate into an existing R Markdown or
    Quarto document.

### Example

Imagine a project where we are using the packages: *dplyr*, *ggplot2*,
*vegan* and *lme4*. We want to collect all the citations listed for
these packages, as well as a citation for base R (and for RStudio, if
applicable).

### Generate a document with formatted citations

Calling `cite_packages()` will scan the project, find these packages,
and generate a document with formatted citations.

``` r
library(grateful)
```

``` r
cite_packages(out.dir = ".")            # save report to working directory
```

![](man/figures/example-output.png)

<br>

This document can also be a Word document, PDF file, markdown file, or
left as the source Rmarkdown file using `out.format`:

``` r
cite_packages(out.format = "docx", out.dir = ".")
```

We can specify the citation style for a particular journal using
`citation.style`.

``` r
cite_packages(citation.style = "peerj", out.dir = ".")
```

In all cases a BibTeX (.bib) file with all package citations will be
saved to disk.

<br>

### Using grateful with Rmarkdown or Quarto

If you are building a document in
[RMarkdown](https://rmarkdown.rstudio.com/) or
[Quarto](https://quarto.org/) and want to cite R packages, **grateful**
can automatically generate a BibTeX file and ensure these packages are
cited in the appropriate format (see template
[Rmarkdown](https://github.com/Pakillo/grateful/blob/master/Rmd_Quarto/grateful-Rmarkdown.Rmd)
and
[Quarto](https://github.com/Pakillo/grateful/blob/master/Rmd_Quarto/grateful-Quarto.qmd)
documents).

First, include a reference to the BibTeX file in your YAML header.

    bibliography: grateful-refs.bib

(Note: You can reference multiple BibTeX files, if needed)

    bibliography: 
      - document_citations.bib
      - grateful-refs.bib

Then call `cite_packages(output = "paragraph")` within a code chunk
(block or inline) to automatically include a paragraph mentioning all
the used packages, and include their references in the bibliography
list.

    ```{r}
    cite_packages(output = "paragraph", out.dir = ".")
    ```

`We used R version 4.2.3 [@base] and the following R packages: lme4 v. 1.1.32 [@lme4], tidyverse v. 2.0.0 [@tidyverse], vegan v. 2.6.4 [@vegan].`

Alternatively, you can get a table with package name, version, and
citations, using `output = 'table'`:

    ```{r }
    pkgs <- cite_packages(output = "table", out.dir = ".")
    knitr::kable(pkgs)
    ```

<img src="man/figures/table.png" width="814" />

If you want the references to appear in a particular format, you can
specify the citation style in the YAML header:

    bibliography: grateful-refs.bib
    csl: peerj.csl

Alternatively, you can cite particular packages using the citation keys
generated by **grateful**, as with any other BibTeX reference, or just
include citations in the References section, using the function
`nocite_references()`. See the package help and the [RMarkdown
cookbook](https://bookdown.org/yihui/rmarkdown-cookbook/bibliography.html)
for more details.

## Frequently Asked Questions

### Getting just a table with used packages and versions

Use `scan_packages`

``` r
scan_packages()
         pkg version
1     badger   0.2.3
2       base   4.3.0
3      knitr    1.42
4    pkgdown   2.0.7
5    remotes   2.4.2
6       renv  0.17.3
7  rmarkdown    2.21
8   testthat   3.1.7
9  tidyverse   2.0.0
10    visreg   2.7.0
```

### Producing a BibTeX file with package references

If you just want to get all package references in a BibTeX file, you can
call `get_pkgs_info()`. Besides printing a table with package info, it
will also save a BibTeX file with references. By default, the file will
be called `grateful-refs.bib`, but you can change that (see function
help).

If you want to get the BibTeX references for a few specific packages:

``` r
get_pkgs_info(pkgs = c("remotes", "renv"), out.dir = getwd())
#>       pkg version citekeys
#> 1 remotes   2.4.2  remotes
#> 2    renv  0.17.3     renv
```

### Using grateful with the tidyverse

If you use one or several packages from the
[tidyverse](https://www.tidyverse.org), you can choose to [cite the
‘tidyverse’](https://www.tidyverse.org/blog/2019/11/tidyverse-1-3-0/#citing-the-tidyverse)
rather than the individual packages:

``` r
cite_packages(cite.tidyverse = TRUE)
```

### Including package dependencies

Most R packages also depend on other packages. To include those package
dependencies in your citations, rather than just the packages you called
directly, use `dependencies = TRUE`:

``` r
cite_packages(dependencies = TRUE)
```

### What about external software dependencies?

Some R packages wrap core external software that should perhaps be cited
too. For example, [`rjags`](https://cran.r-project.org/package=rjags) is
an R wrapper to the [JAGS](https://mcmc-jags.sourceforge.io/) software
written in C++. Ideally, R packages wrapping core external software will
include them in their CITATION file. But otherwise, we can investigate
external software requirements of our used packages, e.g. using
`remotes`:

``` r
remotes::system_requirements(package = c("rjags"), os = "ubuntu-20.04")
#> [1] "apt-get install -y jags"
```

### What software to cite?

Citing software is pretty much like citing papers. Authors have to
decide what to cite in each case, which depends on research context.

As written in the Software Citation Principles paper ([Smith et
al. 2016)](https://doi.org/10.7717/peerj-cs.86):

> The software citation principles do not define what software should be
> cited, but rather how software should be cited. What software should
> be cited is the decision of the author(s) of the research work in the
> context of community norms and practices, and in most research
> communities, these are currently in flux. In general, we believe that
> software should be cited on the same basis as any other research
> product such as a paper or book; that is, authors should cite the
> appropriate set of software products just as they cite the appropriate
> set of papers, perhaps following the FORCE11 Data Citation Working
> Group principles, which state, “In scholarly literature, whenever and
> wherever a claim relies upon data, the corresponding data should be
> cited”

And these are the guidelines from the [Software Citation
Checklist](https://doi.org/10.5281/zenodo.3479198):

> You should cite software that has a significant impact on the research
> outcome presented in your work, or on the way the research has been
> conducted. If the research you are presenting is not repeatable
> without a piece of software, then you should cite the software. Note
> that the license or copyright of the software has no bearing on
> whether you should cite it.

> This might include:

> Software (including scripts) you have written yourself to conduct the
> research presented. A software framework / platform upon which the
> software you wrote to conduct the research relies. Software packages,
> plugins, modules and libraries you used to conduct your research and
> that perform a critical role in your results. Software you have used
> to simulate or model phenomena/systems. Specialist software (which is
> not considered commonplace in your field) used to prepare, manage,
> analyse or visualise data. Software being evaluated or compared as
> part of the research presented Software that has produced analytic
> results or other output, especially if used through an interface.

> In general, you do not need to cite:

> Software packages or libraries that are not fundamental to your work
> and that are a normal part of the computational and scientific
> environment used. These dependencies do not need to be cited outright
> but should be documented as part of the computational workflow for
> complete reproducibility. Software that was used during the course of
> the research but had no impact on research results, e.g. word
> processing software, backup software.

Apart from citing the software most relevant to the particular
research/analysis performed, I think it is good idea to record the
entire computational environment elsewhere, e.g. using `sessionInfo()`
or `sessioninfo::session_info()`.

### Removing unused packages

Before running `grateful` you might want to run
[`funchir::stale_package_check`](https://cran.r-project.org/package=funchir)
or [annotater](https://cran.r-project.org/package=annotater) to check
for unused packages before citing them.

### Error: there is no package called…

If getting an error like “Error in (function (pkg, lib.loc = NULL):
there is no package called…”, that means that some of your scripts is
loading a package that is no longer available in your computer, so
{grateful} cannot grab its citation. To fix this, try checking if that
package is still needed for your project and you want to cite it,
otherwise remove or comment that line where the package is loaded. If
you still use and want to cite that package, install it, and then run
`cite_packages` again.

### Citing ‘grateful’

``` r
citation("grateful")
To cite package 'grateful' in publications use:

  Rodriguez-Sanchez F, Jackson CP, Hutchins SD (2023) grateful:
  Facilitate citation of R packages.
  https://github.com/Pakillo/grateful

A BibTeX entry for LaTeX users is

  @Manual{,
    title = {grateful: Facilitate citation of R packages},
    author = {{Francisco Rodríguez-Sánchez} and {Connor P. Jackson} and {Shaurita D. Hutchins}},
    year = {2023},
    url = {https://github.com/Pakillo/grateful},
  }
```

## Limitations

Citation keys are not guaranteed to be preserved when regenerated,
particularly when packages are updated. This instability is not an issue
when citations are used programmatically, as in the example above. But
if references are put into the text manually, they may need to be
updated periodically.
