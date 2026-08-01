---
name: paper-read-kg
description: |
  Academic paper deep reading and knowledge graph construction. Use this skill
  whenever the user wants to read or analyze academic/scientific PDF papers,
  extract structured summaries from research papers, build paper knowledge
  graphs, discover cross-paper relationships, or process batches of academic
  papers for literature review. Triggers on: paper reading, paper analysis,
  research paper summary, knowledge graph from papers, batch paper processing,
  literature review, paper extraction, 论文阅读, 论文分析, 知识图谱.
---

# Paper Read KG

Unified pipeline for academic paper processing: from PDF → structured abstract
→ Obsidian knowledge graph. Works for a single paper or a batch of any size —
each phase scales naturally. Entirely prompt-driven, no external build scripts.

## Pipeline Overview

```
PDF(s)  ──→  Phase 1: Discover  ──→  Phase 2: Deep Read + KG  ──→  Phase 3: Intra-Group
                                                                      Phase 4: Cross-Domain
                                                                      Phase 5: Finalize & Verify
```

For a **single paper**, Phase 1 is a quick scan to determine category/ID, then
jump directly to Phase 2 (deep read + write both `abstracts/` and `knowledge-base/`
outputs). Phases 3–4 are lightweight for single-paper scenarios.

For a **batch**, all 5 phases run in full: group → parallel deep read per group →
intra-group analysis → cross-domain synthesis → verification + navigation hubs.

---

## Phase 1: Discover & Organize

1. **Scan PDFs** — list all `.pdf` files in the target directory. Count them.
2. **Quick-pass each PDF** — extract the first 2–3 pages of text (title, abstract,
   introduction) with pdfplumber. Read enough to determine:
   - Full title and authors
   - Primary research domain/category
   - Year (from metadata or first-page header)
3. **Auto-group** — assign each paper to a category based on its detected domain.
   Use the category list in `references/abstract-prompt.md` or create a new
   category when genuinely needed. Name group directories as
   `abstracts/<NN>-<category-slug>/`.
4. **Assign IDs** — number papers sequentially across all groups (1, 2, 3, …).
   Record the mapping in `abstracts/_MANIFEST.md`:
   ```
   # Paper Manifest
   | ID | Group | Filename | Title | Authors | Year |
   |----|-------|----------|-------|---------|------|
   | 1  | 00-complex-network | 01-xxx.md | ... | ... | ... |
   ```

---

## Phase 2: Deep Read & Knowledge Graph Build

Process groups sequentially. Within each group, launch **one sub-agent per paper
in parallel** to keep each paper's analysis context independent.

Each sub-agent does the following:

### 2.1 Load Extraction Rules

Read `references/abstract-prompt.md` — the canonical extraction specification.
Follow it exactly for the structured output format.

### 2.2 Extract & Read the PDF

- Use `pdfplumber` (from the `pdf` skill) for text extraction
- For formula-dense, table-heavy, multi-column, or scanned/image-based pages:
  render to PNG via `pdftoppm` and read visually
- For Chinese PDFs: verify encoding correctness via visual spot-checks

### 2.3 Produce Structured Output

YAML frontmatter + 7 fixed sections per `references/abstract-prompt.md` template:

```yaml
---
title: "paper title"
title_zh: "中文译名"
authors: ["author1", "author2", "corresponding*"]
year: 2024
category: "research_domain"
tags: [tag1, tag2, tag3]
lang: "en"
---
## Research Objective
...
## Method
...
## Innovation Points
...
## Dataset
...
## Evaluation Metrics
...
## Key Conclusions
...
## Relationships to Other Papers
...
```

### 2.4 Tag & Entity Normalization

- **Tags**: pick 3–5 from `references/tag-vocabulary.md`. If a genuinely novel
  concept appears, derive a tag (snake_case) and note it.
- **Entities**: normalize method names, datasets, metrics per
  `references/entity-extraction-rules.md`.
- **Self-check**: run the embedded checklist in `abstract-prompt.md` Section 6.

### 2.5 Write Output Files

Write **two files** for each paper:

**File 1 — Abstract:** `abstracts/<group>/<NN>-<paper-name>.md`
- Full structured content as specified above
- Cross-references use `#N` paper ID notation (e.g. "see #5 for comparison")

**File 2 — Knowledge-base vault file:** `knowledge-base/<NN>-<title>.md`
- Same YAML frontmatter
- `## Related Papers` section uses Obsidian `[[wiki-link]]` syntax pointing to
  other papers by their knowledge-base filename (e.g. `[[05-DDMF|#5 DDMF]]`)
- Each link includes a brief relationship description
- `## Tags` section uses `#tag/xxx` Obsidian tag format
- Body content same as abstract

### 2.6 Wiki-Link Rules

Determining which papers to link to (use manifest for `#N → filename` resolution):
- **Shared tags** (≥2): papers in the same method family or domain
- **Shared authors**: same author group, especially advisor-student chains
- **Explicit citations**: paper directly cited by or citing this work
- **Method lineage**: predecessor/successor in the same method evolution chain
- **Bi-directional rule**: if A links B, B should also link A. Coordinate via
  the manifest — when later papers are processed, update earlier papers' links.

---

## Phase 3: Intra-Group Association

After all papers in a group complete, load their YAML frontmatter and key
sections into context. Run the prompt from `references/group-association-prompt.md`.
Write the result to `abstracts/<group>/_INDEX.md`.

Topics to cover: method evolution chain, shared datasets/baselines/theories,
method comparison matrix, author network.

---

## Phase 4: Cross-Domain Analysis

After all groups complete, load all `_INDEX.md` files (they are compact summaries).
Run the prompt from `references/cross-domain-prompt.md`. Write the result to
`abstracts/_CROSS_DOMAIN.md`.

---

## Phase 5: Finalize & Verify

Run a final verification pass using prompts:

1. **Link completeness** — load all knowledge-base files, verify bi-directional
   links. Fix dangling or one-way links with targeted edits.
2. **Navigation hubs** — write via prompts:
   - `knowledge-base/MOC-Master.md` — 4-dimension index (by direction, by method,
     by author, cross-domain)
   - `knowledge-base/_STATS.md` — statistical dashboard
   - `knowledge-base/MOC-<category>.md` — per-category quick-index
3. **Connectivity check** — every paper must have ≥2 wiki-links. For isolated
   papers, add links based on shared tags or authors.
4. **Copy group indexes** — copy `abstracts/<group>/_INDEX.md` to
   `knowledge-base/<group>/_INDEX.md`, fixing `#N` → `[[wiki-link]]`.

The knowledge-base is now a complete Obsidian vault. Open it with Obsidian to
explore the graph view.

---

## Dependencies

| Dependency | Purpose |
|---|---|
| `pdf` skill | Provides `pdfplumber` (text extraction) and `pdftoppm` (page rendering) |
| Python: `pdfplumber`, `pypdf` | PDF text extraction |
| System: Poppler | `pdftoppm` for rendering formula-dense or scanned pages |
| System: Tesseract (optional) | OCR fallback for fully scanned PDFs with no embedded text |

---

## Reference Files

| File | Purpose |
|---|---|
| `references/abstract-prompt.md` | Canonical extraction rules: role, YAML template, 7-section body, behavior rules, 12-item self-check |
| `references/tag-vocabulary.md` | Standardized tag vocabulary (~80 tags across 13 domains) |
| `references/entity-extraction-rules.md` | Normalization rules for method names, datasets, metrics, authors |
| `references/group-association-prompt.md` | Intra-group association discovery prompt template |
| `references/cross-domain-prompt.md` | Cross-domain association discovery prompt template |

---

## Edge Cases & Operational Notes

### PDF extraction quality
- Modern PDFs with embedded text: pdfplumber extracts reliably.
- Scanned/image-based PDFs: render all pages with `pdftoppm`, read visually.
- Formula-dense pages: render with `pdftoppm -png -f N -l N`, read visually to
  capture LaTeX accurately.
- Chinese PDFs: spot-check rendered pages to verify encoding correctness.

### Parallel sub-agent execution
- If some agents fail to launch ("temporarily unavailable"), retry individually.
- The limiting factor is safety classifier availability, not context capacity.

### Single-paper workflow
- Phase 1 reduces to: scan → determine category → assign ID #1.
- Phases 3–4 are lightweight (one-paper intra-group is trivial, one-group
  cross-domain is skipped or minimal).
- Still produce both `abstracts/` and `knowledge-base/` outputs.

### Knowledge graph linking
- Knowledge-base files with `[[wiki-link]]` are written directly by sub-agents
  during Phase 2 — no post-processing script needed.
- Navigation hubs are generated via prompts in Phase 5.
- Bi-directional link rule: when a later paper in the same group links to an
  earlier one, update the earlier paper's vault file to add the reverse link.
