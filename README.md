# DESeq2 Input Prep

A browser-based tool for building a DESeq2-ready RDS file from a raw count matrix and sample metadata — no R installation required.

> **Built by [@bixBeta](https://github.com/bixBeta)**
> **Live demo:** https://bixbeta.github.io/deseq2-prep

---

## What it does

1. **Upload a count matrix** — single matrix file (genes × samples) **or** multiple per-sample count files that are merged automatically
2. **Define sample metadata** — type group assignments directly in the browser, or import an existing metadata file
3. **Download an RDS** — a `list(counts, metadata)` R object ready for upload to [DESeq2 ExploreR](https://github.com/bixBeta/deseq2-explorer)

Everything runs entirely in your browser via [WebR](https://webr.r-wasm.org) (R compiled to WebAssembly). No data is sent to any server.

---

## Input format

### Count matrix (Step 1)

#### Option A — Single matrix file

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

Both formats are detected automatically. Column headers are cleaned of common tool suffixes (see below).

#### Option B — Multiple per-sample files

Drop or select multiple files at once — one file per sample. Each file should have two columns: gene ID and count. The first column is always treated as the gene key regardless of the header name.

```
# e.g. SRR001.ReadsPerGene.out.tab
ENSG00001  142
ENSG00002  0
```

Files are merged by gene ID (union across all files); missing values are filled with **0**. STAR/HTSeq summary rows (`__` / `N_` prefixes) are skipped automatically.

#### Suffix stripping

The following suffixes are automatically stripped from file names and column headers to form clean sample names:

| Tool | Suffixes stripped |
|------|-------------------|
| **STAR** | `.ReadsPerGene.out.tab`, `.ReadsPerGene.out` |
| **featureCounts** | `.featureCounts`, `_featureCounts` |
| **HTSeq-count** | `.htseq-count`, `.htseq`, `_htseq` |
| **Generic** | `.rawCounts`, `_rawCounts`, `.counts`, `.count` |
| **Extensions** | `.txt`, `.tsv`, `.csv`, `.tab`, `.out` |

Both `.` and `_` separators are handled.

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
