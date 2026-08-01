# paper-read-kg

Academic paper deep reading and knowledge graph construction skill for Claude Code.

## Overview

`paper-read-kg` is a unified pipeline skill for processing academic papers: from PDF → structured abstract → Obsidian knowledge graph. It handles single papers or batches of any size.

## Pipeline

```
PDF(s)  ──→  Phase 1: Discover  ──→  Phase 2: Deep Read + KG  ──→  Phase 3: Intra-Group
                                                                   Phase 4: Cross-Domain
                                                                   Phase 5: Finalize & Verify
```

| Phase | Description |
|-------|-------------|
| **Phase 1: Discover** | Scan PDFs, quick-pass for metadata (title/authors/year/domain), auto-group by category, assign IDs |
| **Phase 2: Deep Read** | Extract full text with pdfplumber (+ pdftoppm for formulas), produce YAML frontmatter + 7-section structured abstract, write knowledge-base vault files with `[[wiki-link]]` |
| **Phase 3: Intra-Group** | Per-group method evolution chains, shared elements analysis, method comparison matrix, author network |
| **Phase 4: Cross-Domain** | Cross-domain method transfer tracking, shared methodology identification, collaboration bridge discovery |
| **Phase 5: Finalize** | Link completeness verification, MOC (Map of Content) navigation hubs, statistical dashboard |

## Output

- **`abstracts/`** — Structured markdown abstracts (YAML frontmatter + 7 sections in Chinese)
- **`knowledge-base/`** — Obsidian-compatible vault with `[[wiki-link]]` bidirectional navigation
- **`abstracts/_MANIFEST.md`** — Paper manifest with IDs, groups, metadata
- **`abstracts/_CROSS_DOMAIN.md`** — Cross-domain association analysis
- **`knowledge-base/MOC-Master.md`** — Four-dimension master index
- **`knowledge-base/_STATS.md`** — Statistical dashboard

## Reference Files

| File | Purpose |
|------|---------|
| `skill.md` | Main skill definition with full 5-phase pipeline |
| `references/abstract-prompt.md` | Canonical extraction rules: role, YAML template, 7-section body, behavior rules, 12-item self-check |
| `references/tag-vocabulary.md` | Standardized tag vocabulary (~80 tags across 13 domains) |
| `references/entity-extraction-rules.md` | Normalization rules for method names, datasets, metrics, authors |
| `references/group-association-prompt.md` | Intra-group association discovery prompt template |
| `references/cross-domain-prompt.md` | Cross-domain association discovery prompt template |

## Dependencies

| Dependency | Purpose |
|------------|---------|
| `pdf` skill (Claude Code) | Provides `pdfplumber` (text extraction) and `pdftoppm` (page rendering) |
| Python: `pdfplumber`, `pypdf` | PDF text extraction |
| System: Poppler | `pdftoppm` for rendering formula-dense or scanned pages |
| System: Tesseract (optional) | OCR fallback for fully scanned PDFs |

## Installation

Copy the `.claude/skills/paper-read-kg/` directory into your Claude Code project:

```
your-project/
└── .claude/
    └── skills/
        └── paper-read-kg/
            ├── skill.md
            └── references/
                ├── abstract-prompt.md
                ├── tag-vocabulary.md
                ├── entity-extraction-rules.md
                ├── group-association-prompt.md
                └── cross-domain-prompt.md
```

Then invoke with `/paper-read-kg` in Claude Code.

## License

MIT
