# Motivation letter

**Article title:** csbewma: An R Package for the Cumulative Standardized Binomial EWMA Control
Chart for Multiple Stream Processes

**Article type:** Add-on package

**Corresponding author:** Faruk Muritala (fmurital@students.kennesaw.edu), Kennesaw State
University

## Why this submission fits The R Journal's requirements for an "Add-on package" article

This article documents **csbewma**, a package published on CRAN (version 1.0.1, DOI
10.32614/CRAN.package.csbewma) that implements the Cumulative Standardized Binomial EWMA
(CSB-EWMA) control chart for monitoring binomial proportions across multiple independent
streams. The submission satisfies the journal's stated expectations for package articles as
follows.

**Novel methodology, not a re-implementation.** csbewma implements an exact, closed-form,
time-varying variance for the EWMA statistic under a binomial data-generating process, developed
in the corresponding author's dissertation research and in two companion working papers
(arXiv:2601.09968 and arXiv:2604.12095). Existing binomial EWMA charts rely on asymptotic
variance approximations that are known to be inaccurate during early-phase monitoring; csbewma is,
to the authors' knowledge, the first CRAN package to offer an exact alternative for this setting,
together with an integrated exact-inference post-hoc identification step for attributing a signal
to specific streams.

**Complete, working software.** The package is installed from CRAN, exports a documented public
API (charting, exact variance/control-limit computation, post-hoc identification, and
ggplot2/patchwork-based plotting), and ships with `.Rd` documentation for every exported object.

**The article follows the journal's structure for package papers**: a statement of the
statistical problem and existing gap, a description of the package's functions and S3 class
design, fully reproducible illustrative examples with live, knitr-evaluated R code and output (no
hand-transcribed console output anywhere in the article), a summary of the simulation evidence
behind the package's default tuning parameters, and an explicit discussion of the method's current
limitations (independent-streams assumption, known in-control parameters, and the reduced
statistical power available for post-hoc attribution immediately after an early signal).

**Reproducibility.** All illustrative code in the article is self-contained, seeded
(`set.seed()`), and runs from the CRAN release of csbewma with no external data dependencies. The
larger simulation studies referenced in the parameter-selection and post-hoc sections come from
the corresponding author's dissertation and the two companion preprints cited above, and are
presented as cited, tabulated results rather than re-run computations, consistent with the
journal's guidance for computationally intensive studies.

We believe this article will be of interest to R Journal readers working in statistical process
control, multiple-stream monitoring, and applied categorical-data methods, and we welcome the
editors' and reviewers' feedback.
