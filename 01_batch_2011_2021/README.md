# Batch 1: 2011-2021

Processing outputs from 149,943 papers queried from EuropePMC (publication years 2011-2021).

---

## Summary Statistics

| Metric | Value |
|--------|-------|
| Input papers | 149,943 |
| Classification positives | 34,279 (22.9%) |
| NER entities | ~100,000 |
| Union papers (linguistic + SetFit) | 16,605 |
| Final resources (aggressive) | 10,822 |
| Final resources (balanced) | 5,979 |
| Final resources (conservative) | 4,172 |
| High-confidence final | 964 |

---

## Directory Structure

```
01_batch_2011_2021/
├── 00_input/                    # Raw EPMC query results
├── 01_paper_sets/               # Set A/B/C paper definitions
├── 02_entity_mappings/          # Paper → entity mappings
├── 03_entity_inventories/       # Unique entity catalogs
├── 04_setfit_inference/         # SetFit classification results
├── 05_deduplication/            # Dedup by profile
│   ├── aggressive/              # Max recall (10,822)
│   ├── balanced/                # Recommended (5,979)
│   └── conservative/            # Max precision (4,172)
├── 06_url_analysis/             # URL similarity analysis
├── 07_false_positive_analysis/  # FP detection
└── 08_final_outputs/            # Final inventories
```

---

## Key Files

| File | Location | Rows | Description |
|------|----------|------|-------------|
| `01_input_query_results.csv` | `00_input/` | 149,943 | Raw EPMC query |
| `01c_set_c_union.csv` | `01_paper_sets/` | 16,623 | Union of linguistic + SetFit papers |
| `04a_setfit_all_results.csv` | `04_setfit_inference/` | 20,879 | All SetFit predictions |
| `05c_set_c_final.csv` | `05_deduplication/aggressive/` | 10,822 | Aggressive dedup result |
| `08c_set_c_union_final.csv` | `08_final_outputs/` | 4,084 | Final union resources |
| `08d_linguistic_high_conf_final.csv` | `08_final_outputs/` | 964 | **High-confidence** resources |

---

## Paper Sets

| Set | Criteria | Papers |
|-----|----------|--------|
| **Set A** | Linguistic score ≥ 3 | 8,683 |
| **Set B** | SetFit confidence ≥ 0.60 | 7,938 |
| **Set C** | Union of A + B | 16,623 |
