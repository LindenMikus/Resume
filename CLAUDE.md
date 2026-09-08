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

The generic resume is the master record and **may run longer than one page**.
It is the pool that tailored versions draw from, so it should hold everything
worth saying, not a trimmed selection. Do not cut material from it to save
space, and do not compress its typography to force a page break.

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

  A tailored resume is **always one page.** Since the generic version may run
  longer, tailoring is as much about selection as rewording: drop the bullets
  least relevant to the posting, merge ones that overlap, and reorder what
  remains so the strongest evidence for that job comes first. Say which bullets
  were dropped and why, rather than cutting silently.

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
`0.45in`) and set `\parskip` for block paragraphs rather than indenting. Set
`pdftitle` / `pdfsubject` in `\hypersetup` to name the company and role, using
the job posting's own title so it matches what their ATS screens for.

Below the rule, a small line carries the date on the left and the hiring
contact's name/title/company on the right. Use `\today{}` for the date rather
than typing one in, so the letter always carries the date it was last compiled.
It renders as "September 7, 2026", matching the format these letters use. If a named contact is known, address
the letter to them and open with a first-name greeting ("Hi Dan,"). If not, put
the team and company on that line (e.g. "Backend Engineering, Appnovation") and
greet the company by name: "Hi \<Company\> team,". Never "Dear Hiring Manager,"
which reads as a template, and not a bare "Hi there," either.

## Writing the cover letter

Ask before writing. The job description tells you what the company wants; only
Linden can tell you which of it he has actually done, and at what depth. Work
through the posting's requirements with him and get specifics — the concrete
mechanism, the real numbers — before drafting a single paragraph. The details
that make these letters land (an idempotency key, backoff with jitter, a
permission tier) never come from the resume; they come from asking.

Rules for the prose, learned the hard way:

- **Short.** Roughly 350--400 words, always one page. Brevity beats coverage;
  a letter does not need to answer every bullet in the posting.
- **Only what he has done.** Never write about gaps, missing skills, limited
  exposure, or things he has only touched in personal projects. No conceding
  what he lacks, however candid it sounds — leave those out entirely.
- **Never invent a detail.** Every number, metric, and outcome must come from
  him. Do not add a plausible-sounding figure to strengthen a sentence.
- **No em-dashes.** Use a full stop, a colon, or a comma instead.
- **Lead each paragraph with why it is being raised.** Name the requirement it
  answers, then give the evidence.
- **Understate rather than assert.** "A good share of my work has been in SQL"
  reads better than "I owned the data layer end to end."
- **Do not attribute every claim to an employer.** Naming the company once or
  twice is enough; the rest can simply be work he did.

Structure that has worked: a one-line opener naming the role, two or three
paragraphs of concrete evidence against the posting's core requirements, a short
paragraph on client-facing or embedded delivery where relevant, and a closing
line on location and interest in the role.

Never close with "I am available immediately" or any other statement of
availability; that is a question for the recruiter to ask. State location, then
name the work itself as the thing worth taking further: "Modernizing a platform
the size of Dayforce's is the work I would be interested in discussing further."

## Building

Compile from inside the company folder — the letters need nothing beyond a
standard TeX install with `carlito`:

```bash
pdflatex cover-letter.tex
```

Root resume is the same: `pdflatex resume.tex`.

### Writing `.tex` files: use the file tools, not the shell

Create and edit `.tex` files with the Write and Edit tools. A shell heredoc
(`cat > file <<'EOF'`) silently collapses every `\\` to a single `\`, and `sed`
or `perl` expressions containing backslashes get mangled the same way.

This failure is quiet and expensive. A collapsed `\\[3pt]` becomes `\[3pt]`,
which LaTeX reads as the start of display math. The file still compiles, exit
code 0, no error — the damage only appears in the rendered PDF, as a garbled
header ("3pt]V ictoria") and swallowed line breaks. Every `\\` in the document
is affected at once: the header, the `\jobheader` macro, each skills row, and
the signature line.

So a clean `pdflatex` run is not evidence the document is correct. After
compiling, check the rendered text and the page count:

```bash
pdftotext resume.pdf - | head -5
```

The header line should read as the name, address and contact details, with no
stray `3pt]` fragments. `pdfinfo resume.pdf` confirms a tailored resume or a
cover letter is still one page.

## Git

`Target` is listed in `.gitignore`, so the whole tree beneath it — every company
folder, letter, tailored resume, and PDF — stays local and is never committed.
Only the generic root resume is public. Do not add anything under `Target/` to
the repo or loosen that ignore rule.

LaTeX build artifacts (`*.aux`, `*.log`, `*.out`, `*.synctex.gz`) and
`resume.pdf` are also ignored. CI compiles `resume.tex` only and never touches
`Target/`.
