# AGENTS.md

LaTeX CV built to PDF (LaTeX + Biber) and DOCX (Pandoc). No package manager,
tests, or linters: **verify changes by compiling both outputs.**

## Verify

```sh
latexmk -pdf -file-line-error -halt-on-error -interaction=nonstopmode main.tex  # PDF
python3 scripts/build_docx.py                                                  # DOCX
latexmk -c main.tex                                                            # clean intermediates
```

- PDF needs a TeX distribution with latexmk + Biber. DOCX needs `pandoc` on PATH (CI pins 3.14); the script is stdlib-only Python 3 and writes `main.docx`.
- No `.gitignore`: build artifacts (`main.aux`, `main.log`, `main.docx`, …) show as untracked — never commit them.

## Structure

- `main.tex` — entry point: contact header, `\addbibresource`, section order via `\input`.
- `misc/typesetting.tex` — all packages, formatting, and custom commands (`\cventry`, `\contactsep`).
- `sections/*.tex` — one CV section each; publication sections draw from `bibliography/*.bib`.
- `scripts/build_docx.py` — converts the expanded LaTeX to DOCX; re-implements the custom macros for pandoc.

## Gotchas

- **New custom commands need two edits**: `misc/typesetting.tex` (PDF) *and* the `macros` string in `scripts/build_docx.py` (DOCX), or DOCX fails while PDF succeeds.
- Publication sections must keep the exact pattern `\begin{refsection}[bibliography/<file>]` + `\nocite{*}` + `\printbibliography[heading=none]` — the DOCX converter only understands this and renders the whole `.bib` file (to hide an entry, remove it from the `.bib`). Adding a publication = append a bib entry; no section edit. New `.bib` files are referenced in the section's `refsection` argument; no `\addbibresource` needed.
- Bib formatting lives in `misc/typesetting.tex`: year-descending sort; a `doi` field automatically suppresses the entry's `url`.
- CI: `build-pdf.yml` and `build-docx.yml` run on every push/PR; pushing a tag publishes `NicholasMSynovic_cv.pdf` / `NicholasMSynovic_cv.docx` to a GitHub Release.

## Conventions

- Starred headings (`\section*{...}`); `\cventry{Institution}{Location}{Position/Degree}{Dates}` with spaced en-dashes (`2024 -- Present`).

See README.md for user-facing setup and update instructions.
