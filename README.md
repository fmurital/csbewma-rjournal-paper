# csbewma-rjournal-paper

Source files for "csbewma: An R Package for the Cumulative Standardized Binomial EWMA Control
Chart for Multiple Stream Processes," submitted to *The R Journal*.

**Authors:** Faruk Muritala, Austin Brown, Dhrubajyoti Ghosh, and Sherry Ni (Kennesaw State
University)

## About

`csbewma` is an R package, available on [CRAN](https://cran.r-project.org/package=csbewma), that
implements the Cumulative Standardized Binomial EWMA (CSB-EWMA) control chart for monitoring
binomial proportions across multiple independent streams, using an exact, closed-form, time-varying
variance rather than the asymptotic approximations used by existing binomial EWMA charts. This
repository contains the R Journal article documenting the package: its methodology, its public API,
worked examples with live console output and control charts, and a summary of the simulation
evidence behind its default tuning parameters.

## Contents

- `csbewma-article.Rmd` — the article source (R Markdown, `rjtools` R Journal format)
- `csbewma-article.pdf` — the built article
- `RJreferences.bib` — bibliography
- `RJournal.sty` — R Journal LaTeX style file
- `_Rpackages.txt` — R package dependencies used to build the article
- `motivation-letter/` — cover letter submitted alongside the article

## Reproducing the PDF

With R and the packages listed in `_Rpackages.txt` installed (including `csbewma` itself, from
CRAN), run:

```r
rmarkdown::render("csbewma-article.Rmd")
```

All code shown in the article — including console output and figures — is generated live from
this source at render time; nothing is hand-transcribed.

## License

`csbewma` is distributed under the MIT license.
