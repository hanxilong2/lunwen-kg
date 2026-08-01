# Cross-Domain Association Discovery Prompt

> After all groups are complete, load all `_INDEX.md` files and key
> YAML frontmatter blocks into context, then run this prompt.

---

## Task: Cross-Domain Association Discovery

Below are summary metadata for all papers in the collection. Identify three types of
cross-domain associations:

### 1. Cross-Domain Method Transfer

Methods from one domain applied to another. Examples:
- Complex network centrality / K-Shell methods used for text keyword
  extraction (TextRank)
- Deep learning (Neural ODE) used for myopia trajectory modeling
- Ensemble learning methods used for ECG diagnosis

Output format:
```
| Transfer ID | Source Domain | Target Domain | Source Paper | Target Paper | Description |
|---|---|---|---|---|---|
| MIG-01 | complex_network | nlp | #4 | #19 | Network centrality concepts transferred to text graph ranking |
```

### 2. Shared Methodology

Papers from different domains using the same underlying mathematical or
algorithmic tools. Examples:
- Multiple papers using information entropy as an uncertainty measure
- Multiple papers based on graph neural network frameworks
- Multiple papers using attention mechanisms

```
| Share ID | Shared Method | Involved Paper IDs | Description |
|---|---|---|---|
| SHARE-01 | information_entropy | #2, #6, #10 | Common use of entropy to measure node uncertainty |
```

### 3. Potential Collaboration Bridges

Two seemingly unrelated domains connected through a cross-domain paper.

```
| Bridge ID | Domain A | Domain B | Bridge Paper | Description |
|---|---|---|---|---|
| BRIDGE-01 | complex_network | biomedicine | #28 protein_interaction | Protein interaction network analysis can leverage complex network centrality methods |
```

### 4. Summary

A final table listing all cross-domain associations, sorted by importance.
