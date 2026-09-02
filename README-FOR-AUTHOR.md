# Read this before submitting

## Round 5 update (READ THIS FIRST — folder cleaned, pushed to GitHub, label-clipping fixed)

Three things happened this session, in order:

**1. Cleaned this folder down to just what the submission needs.** Removed all regenerable knit
byproducts (`csbewma-article.html/.tex/.log`, `RJwrapper.tex/.log`, the `csbewma-article_files/`
figure-cache folders, the leftover `csbewma-article.R`) and two unused PNGs from an old
pre-`rjtools` draft (`figures/figure1_plot.png`, `figures/figure2_plot.png`). What's left is
exactly: `csbewma-article.Rmd`, `csbewma-article.pdf`, `RJreferences.bib`, `RJournal.sty`,
`_Rpackages.txt`, `README.md`, `README-FOR-AUTHOR.md`, `HOW-TO-EDIT-AND-REKNIT.md`,
`motivation-letter/motivation-letter.md`, and empty `data/`/`scripts/` placeholders for any
reproducibility files you still want to add before submitting.

**2. Pushed everything to `github.com/fmurital/csbewma-rjournal-paper`.** That repo only had
Round 1's files in it before now (a stale README stub, an old bibliography, and an early draft of
the `.Rmd`); this session's push brought it up to the current, verified state — same file set as
this folder, plus a real `README.md` so the repo's GitHub landing page actually shows something
useful instead of the one-line placeholder it had.

**3. Fixed the "Signal at t = ..." label clipping in Figures 1, 2, and 4** that Round 4 flagged
but left alone. The cause, confirmed by reading `plot.R`: `plot.csb_ewma()` and
`plot_csb_ewma_direct()` both draw that label with `annotate("text", ..., hjust = 0)`, which
left-aligns the text starting at the signal point; since the signal in this article's examples
falls near the right end of the plotted series, the label ran past the panel's right edge and got
clipped by ggplot2's default panel clipping. Rather than edit your installed CRAN package directly
(this session only has access to this paper folder, not your package's source repository), I added
a small, clearly-commented override chunk near the top of `csbewma-article.Rmd` (right after
`load-packages`) that redefines `plot.csb_ewma()`, `plot_csb_ewma_direct()`, and
`plot_chart_with_flagged()` for this document's build only, changing just `hjust = 0` to
`hjust = 1` in the signal-label `annotate()` call in each — right-aligning the label so it ends at
the signal point instead of starting there. Nothing else in those functions changed: no data,
statistic, axis, or numerical result anywhere in the article is affected, only where that one text
label sits. I re-knit the full document after adding it and read all 19 pages of the resulting PDF
again: Figures 1, 2, and 4 now show the complete "Signal at t = ..." label fully inside the panel,
page count is still 19, and nothing else changed.

**For your next CRAN release:** the real fix belongs in your package's own `plot.R`, in both
`plot.csb_ewma()` and `plot_csb_ewma_direct()` — change `hjust = 0` to `hjust = 1` in the
`annotate("text", ...)` call that draws the signal label (the block right after
`geom_point(data = signal_data, ...)` in each function). That's the only change needed; once it's
in the package, the override chunk in this `.Rmd` becomes redundant and can be deleted.

**Round 4's "one cosmetic issue" note above is now resolved** — see Round 4 below for what it
originally described.

## Round 4 update (READ THIS FIRST — the paper now knits to a verified PDF)

Working live in your RStudio this session (with your permission, driving the screen directly), I
took this from "knit fails" to a real, complete, 19-page `csbewma-article.pdf` that I opened and
read page by page. Everything below is what I actually did and actually saw — not a plan, a
completed result as of this session.

**What was broken, and what I changed to fix each thing:**

1. **`xfun` was stuck at 0.56, rmarkdown needs >= 0.60, and reinstalling it kept failing with a
   Windows file-lock (`Permission denied` on `xfun.dll`).** Fixed by pointing `R_LIBS_USER` at a
   fresh library folder (`C:/Users/fmurital/R_newlibs`, added to your `.Renviron`) so `xfun` could
   install clean there without touching the locked copy.
2. **`ggplot2` 4.0's new internal object system (S7) is currently incompatible with `patchwork`
   when `patchwork` is only loaded *inside* a function** (exactly how `plot_chart_with_flagged()`
   loads it) — every combination I tried of ggplot2 4.0.3/patchwork 1.3.2 and ggplot2
   3.5.2/patchwork 1.2.0 still failed with `Can't add p2 to a <ggplot> object` when the combining
   happened inside that function's own call frame, even though the identical call worked fine
   typed directly at the console. The fix that actually worked: add `library(patchwork)` to the
   very top of the document (the `load-packages` chunk), so it's attached before any chunk runs
   rather than being loaded for the first time deep inside a function. I did **not** change your
   installed `csbewma` package — this is purely a load-order fix in the `.Rmd` itself. I also
   settled on ggplot2 3.5.2 (pre-S7) with patchwork 1.2.0 as the safer pairing for this document
   going forward, since that's what I verified against.
3. **No LaTeX distribution was installed at all** (`pdflatex` not found), so the wrapper LaTeX
   compile step had nothing to run. I installed TinyTeX (via a direct GitHub-releases download,
   since the tinytex package's default download URL currently 404s — a temporary issue on their
   end, not yours).
4. **The real bug that was actually breaking the PDF, once the above were fixed:** the two new
   figure captions I added in Round 3 (for `plot_flagged_streams()` and `plot_chart_with_flagged()`)
   had unescaped underscores — `plot_flagged_streams`, `full_results`, `plot_chart_with_flagged` —
   left as plain text instead of `plot\_flagged\_streams` etc. LaTeX reads a bare `_` outside math
   mode as "start a subscript," which cascaded into a `Missing $ inserted` fatal error right at the
   end of the caption. This is the exact same class of bug Round 1 fixed elsewhere; I'd
   reintroduced it in the two captions I wrote fresh in Round 3. Fixed by escaping every underscore
   in both captions, matching how `identify\_ooc()` was already escaped correctly elsewhere in the
   same captions.

**What I verified, by actually opening the resulting PDF and reading all 19 pages myself:**

- All four figures render correctly: the two control charts (Figures 1–2), the new stream p-value
  bar chart (Figure 3), and the new combined dashboard (Figure 4) — all real output from live code,
  not placeholders.
- Tables 2, 5, 6, and 7 (the ones with the `kableExtra` width fix) all fit inside the page margins
  now — no clipped/overflowing columns.
- All four ORCiDs and affiliations appear correctly on the last page.
- The `sessionInfo()` block at the end (page 18) is real, live output from this exact render:
  R 4.5.2, ggplot2 3.5.2, patchwork 1.2.0, kableExtra 1.4.1, csbewma 1.0.1 — confirming every code
  chunk in the document actually executed rather than being pasted in.
- Page count: **19 of the R Journal's 20-page limit.**

**One cosmetic issue I noticed and did not fix, so you can decide:** in Figures 1, 2, and 4, the
red "Signal at t = ..." text label gets clipped at the right edge of the plot panel (you'll see
"Sig" or "Si" cut off instead of the full label). This comes from `plot_csb_ewma_direct()`'s
`annotate("text", ..., hjust = 0)` call inside the package itself — ggplot2's default panel
clipping cuts off text that extends past the panel boundary, and left-justified text starting near
the right edge runs off. This is a genuine, small bug in the *installed package's* plotting
function, not something in the paper's `.Rmd`. Since it's package code, not documentation, I did
not change it here — options if you want it fixed: (a) live with it (it's minor and the annotation
is still informative alongside the diamond marker), (b) shorten the label or use `hjust = 1` in a
future package update, or (c) I can show you exactly where in `plot.R` to change it if you want to
patch the package before your R Journal submission.

**Everything from Round 3 below this point is now superseded** by the fact that the file
successfully knits — treat the numbered list of Round 3 changes as historical record of what was
edited, not as still-open action items.


## Round 3 update (read this first)

I found evidence in your OneDrive folder that a knit of this file was started —
`csbewma-article_files/figure-html5/` has real generated images for the first three figures
(`example1-plot-1.png`, `example2-plot-1.png`, `example2-flagged-plot-1.png`) but nothing for the
fourth (the `plot_chart_with_flagged()` dashboard), and there's no finished `.html`/`.pdf` sitting
next to them yet. If that was you running chunks one at a time, this is just where you'd left off;
if you ran a full knit and it stopped there, tell me what the error said. Either way, I found and
fixed a real bug in that last figure before you get to it:

**`plot_chart_with_flagged()` prints three plots, not one.** This is in the package's own
`plot.R` source, not something introduced by my edits: the function builds its combined dashboard
by calling `plot_csb_ewma_direct()` and `plot_flagged_streams()`, and both of those helper
functions call `print()` on their own plot as a side effect before returning it. So a single call
to `plot_chart_with_flagged()` actually displays three images in sequence — the chart alone, the
bar chart alone, and then the combined dashboard — which, inside a knitr chunk, would have shown
up as three separate, uncaptioned figures crowding together instead of the one clean dashboard
figure the section describes. I added `fig.keep = "last"` to that chunk's options, which keeps
only the final combined image (the function's actual intended output) and discards the two
redundant intermediate prints, and added a sentence explaining this in the surrounding prose so
it doesn't read as unexplained. This is a real quirk of the installed package, not a documentation
error — worth knowing about if you ever call `plot_chart_with_flagged()` interactively too.

I also went through the whole `.Rmd` end to end again and removed two internal
"note to author"-style code comments (in the `setup` and `load-packages` chunks) that were written
in an assistant-to-you voice — they were only ever inside `include=FALSE` chunks so they never
appeared in any rendered PDF, but since the raw `.Rmd` is itself one of the source files the
journal's submission zip requires, I rewrote them as plain, ordinary code comments so nothing in
the file you submit reads as anything other than your own working notes.

**Still open, not something I'll resolve myself:** your dissertation cites Montgomery (2019) as
*"Statistical Quality Control: A Modern Introduction," 6th ed.* (the Wiley India / international
edition), but your companion arXiv preprint cites *"Introduction to Statistical Quality Control,"
8th ed.* (the standard US edition) for the same author/year. `RJreferences.bib` currently follows
the preprint's version. Let me know which one you want and I'll make the `.bib` match.

## Status, honestly stated

This folder has been through three rounds of work:

**Round 1 (verified by an actual knit).** In an earlier session, `csbewma-article.Rmd` was
knitted successfully inside your Posit Cloud project ("RJournal") using `rjtools`. I visually
inspected all pages of the resulting `csbewma-article.pdf` myself. That pass fixed three real
LaTeX build failures — unescaped underscores in two `knitr::kable(caption = ...)` strings and one
`fig.cap = ...` chunk option, which LaTeX misreads as a subscript operator outside math mode — and
replaced 8 broken `\@ref(tab:xxx)` cross-references (not supported by this template) with
hard-coded table numbers matching the tables' actual sequential order in the document.

**Round 2 (fixed in the source, NOT yet re-knitted by me).** In this session you sent screenshots
of the Round-1 PDF showing wide tables running off the page edge, and asked for the ORCIDs, a fix
for stray `alt=...` text appearing above the two figures, a demonstration of the two visualization
functions that were documented but never actually called (`plot_flagged_streams()`,
`plot_chart_with_flagged()`), and a fresh package folder. I made all of those changes directly in
`csbewma-article.Rmd` (details below), but **this session's browser bridge to your logged-in Posit
Cloud / GitHub session was not available, and direct `git push` from my own sandbox is blocked by
its network policy (confirmed by testing it again this session)**. So unlike Round 1, I have not
re-knitted this version myself and cannot show you a verified PDF for it yet. Please knit it
yourself as the first step (see `HOW-TO-EDIT-AND-REKNIT.md`) before you treat it as final, and let
me know what the log says if anything doesn't come out right — table/figure LaTeX issues are
usually a one-line fix.

## What changed in Round 2

1. **ORCIDs added** for all four authors in the YAML `author:` list: Faruk Muritala
   (0000-0002-5857-9874), Austin Brown (0000-0003-1530-0009), Dhrubajyoti Ghosh
   (0000-0002-3360-3786), Sherry Ni (0000-0003-0634-0025).
2. **`fig.alt=` removed** from both figure chunks (`example1-plot`, `example2-plot`). Knitr's
   LaTeX writer doesn't have anywhere to attach alt text (that's an HTML/accessibility attribute),
   so it was falling back to printing the alt string as a literal `alt=...` line of body text
   directly above each figure. Removing the option is the standard fix; the `fig.cap=` captions,
   which do render correctly, are unaffected.
3. **Wide tables fixed with `kableExtra`.** Table 2 (functions by group), Table 5 (optimal
   lambda/L pairs, 7 columns), Table 6 (ARL1 by distribution), and Table 7 (post-hoc method
   performance) now pipe through `kableExtra::kable_styling(latex_options = c("scale_down",
   "hold_position"))`, which shrinks the whole table to fit the page width via `\resizebox`. Table
   2 and Table 7 additionally use `kableExtra::column_spec()` on their widest text columns so long
   strings (function names, the "Sensitivity (rank)" descriptions) wrap onto multiple lines
   instead of running off the page. **This adds a new dependency, `kableExtra`** — it's now listed
   in `_Rpackages.txt`, and the `load-packages` chunk has a commented-out one-line installer if you
   don't have it yet.
4. **New subsection "Visualizing flagged streams"** added right after the second illustrative
   example (before "Chart parameter selection and detection performance"). It calls
   `plot_flagged_streams(full_results, alpha = 0.05)` and `plot_chart_with_flagged(result2,
   full_results, layout = "side")` — the two plotting functions that Table 2 and the Visualization
   subsection already described but that no earlier draft ever actually ran. Both calls were
   checked against the exact argument names and order in `plot_flagged_streams.Rd` and
   `plot_chart_with_flagged.Rd` before I added them, so they should knit; the prose explains why
   `full_results` (the fuller 500-column post-hoc pass) is paired with `result2` (the chart object
   from the earlier signal at t = 33) — it's an intentional pairing that ties back to the paper's
   point about attribution improving with more post-signal data, not a mismatch.

## On the `figures/` folder — you asked whether this is fine

Yes, it's fine as it is, and here's exactly why: `figures/figure1_plot.png` and
`figures/figure2_plot.png` are leftovers from the very first, pre-rjtools LaTeX draft of this
paper. I checked — `csbewma-article.Rmd` contains zero references to any `.png` file. Every figure
in the current article (now four: the two control charts plus the two new flagged-stream plots) is
produced live by a `plot(...)` / `plot_flagged_streams(...)` / `plot_chart_with_flagged(...)` call
inside an evaluated code chunk at knit time, which is what you want for the "R outputs are from an
evaluation of R code" requirement. The PNGs in `figures/` are simply unused; you can leave them
there for your own reference or delete them, either is safe. `data/` and `scripts/` are likewise
empty placeholders from the `rjtools` folder convention — the article has no external data
dependency beyond seeded `rbinom()` draws.

## On the "0.3 / 0.4"-style section numbers you may see

If your knitted PDF shows section headers like "0.3 The csbewma package" or "0.4 Illustrative use
of the package" rather than "3" / "4", that is very likely the `rjtools::rjournal_pdf_article`
template's own numbering behavior (its LaTeX class numbers top-level Markdown `##` headers as
subsections of an implicit, unlabeled top section), not something introduced by any of my edits —
I did not change how the document's headers are leveled, and the section order and nesting in
`csbewma-article.Rmd` (`##` for the eight main sections, `###` for their subsections) is unchanged
from Round 1. I'd rather flag this and let you confirm it against a real published R Journal
article or the rjtools template docs than silently change heading levels and risk breaking
something the template expects.

## Files in this folder

- `csbewma-article.Rmd` — the manuscript (Round 2 state, described above).
- `RJreferences.bib` — bibliography, unchanged since Round 1.
- `motivation-letter/motivation-letter.md` — draft motivation letter for the "Add-on package"
  submission type.
- `_Rpackages.txt` — R packages needed to knit the article (now includes `kableExtra`).
- `figures/` — two unused reference PNGs from the original LaTeX draft (see above).
- `data/`, `scripts/` — empty placeholders per the rjtools folder convention.
- `HOW-TO-EDIT-AND-REKNIT.md` — step-by-step guide for proofreading, editing, and re-knitting this
  locally in RStudio.

## Before you submit

Run the journal's own checks, which are the authoritative source on formatting rules (title case,
section-heading case, spelling) — I have not run these myself:

```r
rjtools::initial_check_article("csbewma-article.Rmd")
rjtools::check_title("csbewma-article.Rmd")
rjtools::check_section("csbewma-article.Rmd")
rjtools::check_spelling("csbewma-article.Rmd")
```

And push your changes to `https://github.com/fmurital/csbewma-rjournal-paper` yourself once you're
happy with a knit — see `HOW-TO-EDIT-AND-REKNIT.md` for the exact commands; I could not push from
this session (see Status above).
