# Repo layout and conventions

This repo holds a generic, public-facing resume at the root, plus per-company
tailored application material under `Target/` that is kept entirely local.

## Structure

```
resume.tex                       # the generic resume — single source of truth
Linden-Mikus-Resume.pdf          # generic resume, compiled (CI publishes this name)
.github/workflows/build.yml      # CI: compiles resume.tex only

Target/                          # git-ignored in full, including every subfolder
  <Company>/
    cover-letter.tex             # required
    resume.tex                   # optional — only if tailored for this posting
```

## The two levels

**Root = generic and public.** `resume.tex` is the baseline resume, kept
company-neutral. It is the only file CI builds and the only resume published to
the GitHub `latest` release. Improvements that apply to every application belong
here.

**`Target/<Company>/` = one folder per company applied to, private.** Everything
inside is specific to that application, never merged back into the root, and
never committed.

## What goes in a company folder

The folder name is the company, so the files inside do not repeat it.

- `cover-letter.tex` — **required.** The only file every company folder must
  have.
- `resume.tex` — **optional.** Add one only when the resume is actually being
  altered for the posting (reordered skills, reworded bullets, emphasis shifted
  toward the job description). Often the generic resume is submitted unchanged,
  in which case the folder has no resume file at all.

Compiled PDFs are build output, not tracked material — generate them by running
`pdflatex` in the folder. A plain-text copy of a letter is sometimes needed for
application forms with a paste-in textarea; generate it ad hoc when required.
There is no expectation that any `.txt` in a folder stays in sync with the
`.tex`, and no need to create one preemptively.

Older folders may not follow this naming — match the convention above for new
ones rather than the files already sitting there, and leave existing folders
alone unless asked.

## Styling: cover letters inherit the resume's look

Cover letter `.tex` files deliberately mirror `resume.tex` so the two documents
read as a set. The shared pieces:

- `\documentclass[10.5pt]{article}`, `carlito` font with `\familydefault` set to
  `\sfdefault`, `T1` font encoding
- the same four colors:
  `accent 1F4E5A`, `accentlight 5B8A99`, `textgray 2B2B2B`, `rulelight 9FB8C0`
- name centered in 22pt bold `accent`, contact line under it, then a `rulelight`
  horizontal rule
- `\pagestyle{empty}`, no paragraph indent

Differences from the resume: cover letters use `margin=0.85in` (the resume uses
`0.5in`) and set `\parskip` for block paragraphs rather than indenting. Set
`pdftitle` / `pdfsubject` in `\hypersetup` to name the company and role.

Below the rule, a small line carries the date on the left and the hiring
contact's name/title/company on the right, then the letter opens with a
first-name greeting.

## Building

Compile from inside the company folder — the letters need nothing beyond a
standard TeX install with `carlito`:

```bash
pdflatex cover-letter.tex
```

Root resume is the same: `pdflatex resume.tex`.

## Git

`Target` is listed in `.gitignore`, so the whole tree beneath it — every company
folder, letter, tailored resume, and PDF — stays local and is never committed.
Only the generic root resume is public. Do not add anything under `Target/` to
the repo or loosen that ignore rule.

LaTeX build artifacts (`*.aux`, `*.log`, `*.out`, `*.synctex.gz`) and
`resume.pdf` are also ignored. CI compiles `resume.tex` only and never touches
`Target/`.
