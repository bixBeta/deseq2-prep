# DESeq2 Input Prep

A browser-based tool for building a DESeq2-ready RDS file from a raw count matrix and sample metadata — no R installation required.

> **Built by [@bixBeta](https://github.com/bixBeta)**
> **Live demo:** https://bixbeta.github.io/deseq2-prep

---

## What it does

1. **Upload a count matrix** — tab-delimited text file (genes × samples)
2. **Define sample metadata** — type group assignments directly in the browser, or import an existing metadata file
3. **Download an RDS** — a `list(counts, metadata)` R object ready for upload to [DESeq2 ExploreR](https://github.com/bixBeta/deseq2-explorer)

Everything runs entirely in your browser via [WebR](https://webr.r-wasm.org) (R compiled to WebAssembly). No data is sent to any server.

---

## Input format

### Count matrix (Step 1)

A tab-delimited (`.txt` / `.tsv`) or comma-delimited (`.csv`) file with genes as rows and samples as columns.

**Standard format** (gene ID column labeled):
```
gene       sample1  sample2  sample3
ENSG00001  142      87       201
ENSG00002  0        12       5
```

**R default format** (blank or missing first header field — from `write.table(...)`):
```
           sample1  sample2  sample3
ENSG00001  142      87       201
ENSG00002  0        12       5
```

Both formats are detected automatically.

### Metadata (Step 2)

Enter group labels for each sample directly in the table. The first column you add is named `group` by default — this is the column DESeq2 ExploreR uses for its design formula.

You can also import an existing tab-delimited metadata file (first column = sample names, remaining columns = variables).

### Output RDS (Step 3)

A named R list saved as an RDS file:

```r
list(
  counts   = matrix,     # genes × samples (integer counts)
  metadata = data.frame  # samples × variables (row names match colnames of counts)
)
```

---

## Deploy to GitHub Pages

This is a single-file static app — just `index.html`. To host it for free on GitHub Pages:

### Option A — New repository

1. Create a new repository on GitHub (e.g., `deseq2-prep`)
2. Upload `index.html` to the repository root
3. Go to **Settings → Pages → Source** and select `main` branch, `/ (root)`
4. Your app will be live at `https://<your-username>.github.io/deseq2-prep`

### Option B — GitHub CLI

```bash
cd /path/to/deseq2-prep-tool
git init
git add index.html README.md
git commit -m "Initial commit"
gh repo create deseq2-prep --public --push --source .
# Then enable GitHub Pages in the repo settings
```

### Option C — Existing repo with `gh-pages` branch

```bash
git checkout --orphan gh-pages
git add index.html README.md
git commit -m "Deploy prep tool"
git push origin gh-pages
```

GitHub Pages serves the `gh-pages` branch automatically — no settings change needed.

---

## Tech stack

| Layer | Technology |
|-------|-----------|
| R runtime | [WebR](https://webr.r-wasm.org) (R 4.x compiled to WebAssembly) |
| UI | Vanilla HTML / CSS / JavaScript (no build step, no dependencies) |
| Hosting | GitHub Pages (static file serving) |

---

## Usage notes

- **First load is slow** — WebR downloads ~8 MB of WASM binaries. Subsequent loads use the browser cache.
- **Large files** — Files up to several hundred MB work fine; the bottleneck is the browser's JS heap and WebR's WASM memory. For very large datasets (> 50k genes, > 200 samples) the browser may be slow.
- **Privacy** — All computation happens locally. Your count data never leaves your machine.

---

## Related

- **[DESeq2 ExploreR](https://github.com/bixBeta/deseq2-explorer)** — the main app that consumes the RDS output from this tool

---

## License

MIT
