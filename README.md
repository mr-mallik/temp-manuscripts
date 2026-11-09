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
│   │   ├── .olignore           # Files overleaf-sync should skip
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
   tectonic main.tex   # produces main.pdf (or: latexmk)
   ```

   To collaborate on Overleaf, see [Syncing with Overleaf](#syncing-with-overleaf).

## Requirements

- A LaTeX engine — either:
  - [Tectonic](https://tectonic-typesetting.github.io/) (recommended: one small binary, downloads packages on demand), or
  - a full distribution: [TeX Live](https://tug.org/texlive/), [MiKTeX](https://miktex.org/) or [MacTeX](https://tug.org/mactex/) (includes `latexmk` and `bibtex`).
- Optional: [overleaf-sync](https://github.com/moritzgloeckl/overleaf-sync) (Python 3.11+) to collaborate with co-authors on Overleaf.
- Optional: Python 3 for anything in `shared/evaluation/`.

## Working locally

### Building with Tectonic

[Install Tectonic](https://tectonic-typesetting.github.io/en-US/install.html), then from a manuscript folder:

```bash
cd journals/<paper-name>
tectonic main.tex                # -> main.pdf (runs BibTeX automatically)
tectonic --keep-logs main.tex    # also keep main.log for debugging
tectonic -Z shell-escape main.tex  # only if you use minted / shell-escape
```

Notes:

- The first build downloads the packages it needs, so it needs internet access; later builds use the local cache.
- Tectonic doesn't leave `.aux`/`.log` clutter behind, and it ignores `latexmkrc` (that file is only for `latexmk`).
- If Tectonic can't find the shared bibliography, copy it into the manuscript folder first (see [Shared files](#shared-files-on-overleaf-and-tectonic)). `main.tex` picks up a local `references.bib` automatically.

### Syncing with Overleaf

[overleaf-sync](https://github.com/moritzgloeckl/overleaf-sync) (`ols`) gives two-way sync between a local folder and an Overleaf project, so you can write in your own editor and use git while co-authors keep working on Overleaf. It is an unofficial tool, not affiliated with Overleaf.

**1. Install and log in (once):**

```bash
pip install --pre "overleaf-sync[gui]"   # --pre: v2 pre-release, needed for Overleaf's current site
ols login                                # opens a login window, saves the cookie to .olauth
```

If the login window doesn't work (e.g. institutional SSO or ORCID login), use `ols login --cookie` and paste your Overleaf session cookie.

> ⚠️ `.olauth` can log in to your Overleaf account. It is git-ignored here; never commit or share it. Use `--store-path` to keep it outside the repo, e.g. `ols login --path ~/.olauth`, then pass `--store-path ~/.olauth` to every `ols` command.

**2. Link a manuscript to an Overleaf project:**

`ols` can't create projects and matches by name: **the Overleaf project must have the same name as the manuscript folder.**

1. On Overleaf, create a blank project named exactly `<paper-name>`.
2. Copy the shared files into the folder (see below).
3. Push the local files up:

   ```bash
   cd journals/<paper-name>
   ols list          # check the project is visible
   ols -l            # first sync: local -> Overleaf only
   ```

**3. Day-to-day:**

```bash
cd journals/<paper-name>
ols               # two-way sync; asks which version to keep on conflicts
ols -r            # pull Overleaf changes only
ols -l            # push local changes only
ols download      # compile on Overleaf and download the PDF
git add -A && git commit -m "Sync with Overleaf"
```

- Wait 1–2 minutes after an edit on Overleaf before syncing, or the change may not be picked up yet.
- Commit before each sync so you can undo a bad merge with git.
- Each manuscript has an `.olignore` (fnmatch patterns, not `.gitignore` syntax; write folders as `build/*`) that keeps build files, `README.md` and `latexmkrc` off Overleaf.

### Shared files on Overleaf and Tectonic

Overleaf only sees what's inside the project folder, so anything under `shared/` must be copied into the manuscript before syncing. `shared/` stays the source of truth.

**Bibliography:** copy it in (the copy is git-ignored):

```bash
# macOS / Linux / Git Bash
cp shared/references/references.bib journals/<paper-name>/
```

```powershell
# Windows PowerShell
Copy-Item shared\references\references.bib journals\<paper-name>\
```

`main.tex` uses the local `references.bib` when it exists and falls back to `shared/references/` otherwise, so the same file works with Overleaf, Tectonic and `latexmk`.

If co-authors add references on Overleaf, copy them back after `ols -r`, then commit:

```bash
cp journals/<paper-name>/references.bib shared/references/references.bib
```

**Figures:** copy any figures you use from `shared/figures/` into the manuscript's own `figures/` folder. LaTeX looks there first, so no changes to `main.tex` are needed.

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
