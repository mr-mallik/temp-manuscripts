# Manuscripts Template

A reusable repository structure for writing and managing academic manuscripts in LaTeX — one folder per paper/journal submission, with figures, references and evaluation code shared across them.

## Repository structure

```
.
├── journals/
│   ├── _template/              # Starter manuscript — copy this for each new paper
│   │   ├── main.tex            # Entry point (preamble, metadata, section includes)
│   │   ├── sections/           # One .tex file per section
│   │   ├── figures/            # Figures specific to this manuscript
│   │   ├── latexmkrc           # Build settings for latexmk
│   │   └── README.md           # Target journal, status and submission log
│   └── <paper-name>/           # Your manuscripts
├── shared/
│   ├── figures/                # Figures reused across manuscripts
│   ├── references/
│   │   └── references.bib      # Single shared bibliography
│   └── evaluation/             # Analysis scripts, notebooks, result tables
├── .editorconfig
├── .gitattributes
├── .gitignore
├── LICENSE
└── README.md
```

## Getting started

1. **Create your own copy** — click **Use this template** on GitHub (or clone and re-point `origin`):

   ```bash
   git clone https://github.com/mr-mallik/temp-manuscripts.git my-manuscripts
   cd my-manuscripts
   git remote set-url origin <your-new-repo-url>
   ```

2. **Start a manuscript** by copying the starter folder:

   ```bash
   cp -r journals/_template journals/<paper-name>
   ```

3. **Fill in** `journals/<paper-name>/README.md` (target journal, limits, status) and edit `main.tex`.

4. **Build** from inside the manuscript folder:

   ```bash
   cd journals/<paper-name>
   latexmk          # produces main.pdf
   latexmk -c       # removes auxiliary files
   ```

## Requirements

- A TeX distribution: [TeX Live](https://tug.org/texlive/), [MiKTeX](https://miktex.org/) or [MacTeX](https://tug.org/mactex/) (includes `latexmk` and `bibtex`).
- Optional: Python 3 for anything in `shared/evaluation/`.

### Overleaf

Overleaf can't see files outside the project root, so to use a manuscript there, upload the manuscript folder together with the bib file and any shared figures it uses, then change the bibliography path in `main.tex` to `\bibliography{references}`.

## Conventions

| Item | Convention |
|---|---|
| Manuscript folders | `journals/<short-paper-name>` or `journals/<journal>-<topic>`, lowercase with hyphens |
| Citation keys | `<firstauthorsurname><year><firstword>`, e.g. `smith2024hybrid` |
| Figures | Vector formats (`.pdf`, `.eps`) where possible; generated figures are written by scripts in `shared/evaluation/` |
| Labels | Prefixed: `sec:`, `fig:`, `tab:`, `eq:` |
| Notes | `\todo{...}` (renders in red) — search for `TODO` before submitting |

### Journal-specific classes

If the journal supplies its own template (e.g. `elsarticle`, `IEEEtran`, `svjour3`, `sn-jnl`), place its `.cls`/`.bst` files in the manuscript folder, swap the `\documentclass` and `\bibliographystyle` lines in `main.tex`, and keep the `sections/` includes as they are.

### Compiled PDFs

`*.pdf` is git-ignored as build output, except inside `figures/` and `supplementary/` folders. To keep a submitted or accepted version, force-add it:

```bash
git add -f journals/<paper-name>/submitted-v1.pdf
```

## Pre-submission checklist

- [ ] No `\todo{}` notes left
- [ ] No undefined references or citations (`latexmk` prints a summary)
- [ ] Word/page count within the journal limit
- [ ] Author list, affiliations, ORCIDs and corresponding author correct
- [ ] Figures at required resolution/format; all referenced in the text
- [ ] Data availability, funding, conflict of interest and CRediT statements included
- [ ] Bibliography style matches the journal's guide for authors
- [ ] Cover letter prepared

## License

Released under the [MIT License](LICENSE). The license covers this template; manuscripts you write with it remain yours.
