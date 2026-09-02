# How to proofread, edit, and re-knit this paper locally

This is the short, practical version. It assumes you're opening this folder in RStudio (Desktop
or Posit Cloud) on a machine that already has R and the `csbewma` package installed, since your
earlier console sessions confirm you have that set up.

## 1. One-time setup

Open this folder in RStudio (double-click `csbewma-article.Rmd`, or File → Open Project if you
turn this into a project first). Then, in the Console, install anything you don't already have:

```r
install.packages(c("rjtools", "ggplot2", "patchwork", "kableExtra", "knitr", "rmarkdown"))
```

`csbewma` itself you already have (it's on CRAN as version 1.0.1). `kableExtra` is new this
round — it's what fixes the tables that were running off the page edge.

## 2. Knit it

Click the **Knit** button at the top of the editor pane (or run
`rmarkdown::render("csbewma-article.Rmd")` in the Console). This produces
`csbewma-article.pdf` in the same folder. Every code chunk in the "Illustrative use of the
package" section actually runs when you do this — the console messages, the printed
`csb_ewma` summaries, and all four figures are generated fresh, not pasted in.

**If it fails**, scroll to the bottom of the Render/Console output for the actual LaTeX error —
it's almost always one specific line, not a cascade. The two bug classes that have bitten this
document before:
- An underscore inside a `caption = "..."` or `fig.cap = "..."` string that isn't escaped as
  `\\_` (LaTeX reads a bare `_` outside math mode as "start a subscript" and throws `Missing $
  inserted`).
- A `\@ref(tab:xxx)` or `\@ref(fig:xxx)` cross-reference — this template doesn't resolve them, so
  every table/figure reference in the prose is now a hand-written plain number ("Table 5", "Figure
  2") instead. If you add or reorder a table or figure, you'll need to update these plain-number
  mentions by hand to match the new order — search the .Rmd for "Table " and "Figure " to find
  them all.

## 3. Proofread the PDF

Read `csbewma-article.pdf`, not the `.Rmd` — the PDF is what a reviewer sees. Things worth
specifically checking on this first local knit, since I could not verify them myself this round:
- The four tables that now use `kableExtra` (Table 2, 5, 6, 7) — confirm nothing is still
  clipped and that the shrunk font size is still readable. If a table looks too small, open the
  `.Rmd`, find its chunk (search for `functions_df`, `optimal_df`, `arl1_df`, or
  `posthocperf_df`), and either widen the `column_spec()` values or drop `"scale_down"` from
  `latex_options` in favor of just the `column_spec()` wrapping.
- The two new figures (`plot_flagged_streams(full_results, ...)` and
  `plot_chart_with_flagged(result2, full_results, ...)`) render as expected and the
  `fig.alt=` text no longer shows up as a stray line of body text above any figure.
- The section numbering style ("0.3", "0.4", etc., or plain "3", "4") — this is a property of the
  `rjtools` template, not something in your control from the `.Rmd` content; just confirm it looks
  like other published R Journal articles.

## 4. Make an edit

Everything is plain text. A few common edits:

- **Wording in the prose**: edit the paragraph text directly, same as any Markdown file.
- **A table's numbers or column names**: find the `data.frame(...)` call just above the matching
  `knitr::kable(...)` (e.g. `optimal_df <- data.frame(...)`) and edit the values there — the
  `kable()` call just displays whatever is in that data frame, so you never need to touch the
  table's LaTeX/Markdown formatting directly.
- **Adding a citation**: add the entry to `RJreferences.bib` in the same format as the existing
  entries, then cite it in the text as `[@yourkey2026]` (matching the `bib` entry's key).
- **A new figure or table**: copy the pattern of an existing chunk. For a table, remember to give
  it a `caption = "..."` and, if any word in the caption has an underscore, escape it as `\\_`. For
  a numbered mention of it in the prose ("Table 9"), count its position among all `knitr::kable()`
  calls in the document, top to bottom — that's the number LaTeX will actually assign it.

After any edit, re-knit (step 2) before moving on, so you catch problems one at a time instead of
stacking several edits before the first knit since your last save.

## 5. Save your changes to GitHub

Your paper's GitHub repository is `https://github.com/fmurital/csbewma-rjournal-paper`. Once
you're happy with a local knit, push your changes from RStudio's **Terminal** tab (not the R
Console):

```bash
git add csbewma-article.Rmd RJreferences.bib
git commit -m "Describe what you changed"
git push origin main
```

If this folder isn't a git clone of that repository yet, clone it fresh first and copy your edited
files in:

```bash
git clone https://github.com/fmurital/csbewma-rjournal-paper.git
cp csbewma-article.Rmd RJreferences.bib csbewma-rjournal-paper/
cd csbewma-rjournal-paper
git add csbewma-article.Rmd RJreferences.bib
git commit -m "Round 2 fixes: ORCIDs, table widths, fig.alt, flagged-stream plots"
git push origin main
```

The rest of this folder (`motivation-letter/`, `_Rpackages.txt`, `README-FOR-AUTHOR.md`,
`figures/`, `data/`, `scripts/`) is worth adding to that same repo too, as a complete backup of
your submission folder — `git add -A` instead of naming files individually will pick up
everything, including this guide.

## 6. When you're ready to submit

Run the journal's own automated checks (see `README-FOR-AUTHOR.md`, "Before you submit"), then
follow The R Journal's submission instructions on their website for the "Add-on package" article
type, attaching the motivation letter from `motivation-letter/motivation-letter.md`.
