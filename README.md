# Resume

Source for my resume, built with LaTeX (pdfLaTeX) and compiled automatically on every push via GitHub Actions.

## Structure

```
resume.tex              # the resume — single source of truth
.github/workflows/       # CI: builds the PDF on every push
.gitignore
```

## Building locally

Requires a TeX distribution — [MiKTeX](https://miktex.org/) (recommended) or [TeX Live](https://www.tug.org/texlive/) — with the `carlito` package, which both distributions include out of the box (installed on demand by MiKTeX, or via `tlmgr install carlito` on TeX Live). No system font installation needed.

Edit and compile directly in [TeXstudio](https://www.texstudio.org/) using the default **PdfLaTeX** compiler, or from the command line:

```bash
pdflatex resume.tex
```

## CI

Every push to `main` (and every PR) triggers `.github/workflows/build.yml`, which compiles `resume.tex` with pdfLaTeX and:

- uploads the PDF (renamed to `Linden-Mikus-Resume.pdf`) as a workflow artifact (zipped, from the Actions run's summary page) on every push and PR
- on pushes to `main`, publishes/updates a `latest` GitHub Release with `Linden-Mikus-Resume.pdf` attached as a direct-download asset — always available at:

  ```
  https://github.com/LindenMikus/Resume/releases/latest/download/Linden-Mikus-Resume.pdf
  ```

## Editing

All content lives in `resume.tex`. Sections are marked with `% ---` comments (Profile, Skills, Experience). Each job entry uses the `\jobheader{title}{dates}{company}{location}` macro followed by an `itemize` block for bullets.
