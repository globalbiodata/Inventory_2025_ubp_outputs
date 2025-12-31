# Baseline Reference Files

Papers and entities that match the existing baseline inventory (3,112 resources from 2022).

## Purpose

These files contain papers and resources that were **excluded from the novel discovery output** because they correspond to resources already present in the baseline inventory. They are preserved for:

1. **Updating existing entries** - New papers may have updated URLs, metadata, or citations
2. **Literature reviews** - Track all papers mentioning known resources
3. **Quality control** - Verify baseline resources are being correctly identified

---

## Files

### From 2011-2021 Batch

| File | Rows | Description |
|------|------|-------------|
| `01a_2011_2021_baseline_by_pmid.csv` | 2,666 | Papers matched by PMID |
| `01b_2011_2021_baseline_by_entity_match.csv` | 4,760 | Papers matched by entity name |
| `01c_2011_2021_baseline_vs_validation.csv` | 3,112 | Full comparison matrix |

### From 2022-mid2025 Batch

| File | Rows | Description |
|------|------|-------------|
| `02a_2022_2025_baseline_matches.csv` | 1,469 | All baseline comparisons |
| `02b_2010_2022_baseline_matches.csv` | 953 | Merged batch baseline matches |

### Consolidated (Final Merged)

| File | Rows | Description |
|------|------|-------------|
| `03a_confirmed_baseline_matches.csv` | 825 | **Confirmed** baseline matches |
| `03b_baseline_high_confidence.csv` | 578 | High-confidence matches |
| `03c_baseline_fuzzy_review.csv` | 192 | Fuzzy matches (need review) |
| `03d_baseline_url_matches.csv` | 312 | URL-based matches |
| `03e_baseline_all_comparisons.csv` | 2,422 | All resources with baseline flags |

---

## Match Types

| Type | Description |
|------|-------------|
| `PMID` | Paper PMID matches baseline paper |
| `NAME_EXACT` | Entity name exactly matches baseline |
| `NAME_FUZZY` | Entity name >80% similar to baseline |
| `NO_MATCH` | No baseline match found (novel) |

---

## Usage Examples

### Find papers about a specific baseline resource
```python
import pandas as pd
df = pd.read_csv('01b_2011_2021_baseline_by_entity_match.csv')
resource_papers = df[df['primary_entity_long'].str.contains('GenBank', case=False)]
```

### Get high-confidence matches for auto-update
```python
high_conf = pd.read_csv('03b_baseline_high_confidence.csv')
# These can be used to automatically update baseline entries
```
