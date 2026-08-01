# Intra-Group Association Discovery Prompt

> After generating abstracts for all papers in a topic group, load the full
> set of abstracts into context and append the following task.

---

## Intra-Group Association Analysis

You have now read all papers in this group. Produce the following three outputs:

### 1. Method Evolution Chain

List the method evolution within this group, ordered by year:

```
YYYY — Paper Title (Author): brief method description
  --> (improves or extends)
YYYY — Paper Title (Author): what was improved / extended from the prior work
  --> (improves or extends)
...
```

Mark which papers are direct improvements of prior work, and which are
independently proposed competing methods.

### 2. Shared Elements

#### Shared Datasets
- List datasets that are shared or partially shared across papers in this group

#### Shared Theoretical Frameworks
- List foundational theories commonly relied upon (graph theory, information
  theory, spreading dynamics, etc.)

#### Shared Baseline Methods
- List methods used as comparison baselines by multiple papers in this group

### 3. Method Comparison Matrix

| Paper ID | Core Method | Method Type | Innovation Dimension | Best Metric | Limitations |
|---|---|---|---|---|---|
| #N | ... | ... | ... | ... | ... |
