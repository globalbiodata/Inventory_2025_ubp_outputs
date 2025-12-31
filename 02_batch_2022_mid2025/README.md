# Batch 2: 2022-mid2025

Processing outputs from 98,571 papers queried from EuropePMC (publication years 2022-mid2025).

---

## Summary Statistics

| Metric | Value |
|--------|-------|
| Input papers | 98,571 |
| Classification positives | 27,978 (28.4%) |
| NER entities | 103,473 (SpaCy: 68,151 + V2: 35,322) |
| Union papers (linguistic + SetFit) | 7,594 |
| Final resources (aggressive) | 5,096 |
| Final resources (balanced) | 1,708 |
| Final resources (conservative) | 1,163 |
| Final with URLs | 1,510 |

---

## Directory Structure

```
02_batch_2022_mid2025/
├── 00_input/                    # Raw EPMC query results
├── 01_classification/           # V2 + PyCaret classification
├── 02_ner/                      # SpaCy + V2 NER results
├── 03_linguistic_scoring/       # Linguistic score papers
├── 04_setfit/                   # SetFit classification
├── 05_entity_mapping/           # Paper → entity mappings
├── 06_url_scanning/             # URL extraction
├── 07_deduplication/            # Dedup by profile
│   ├── aggressive/              # Max recall (5,096)
│   ├── balanced/                # Recommended (1,708)
│   └── conservative/            # Max precision (1,163)
├── 08_url_recovery/             # URL recovery from fulltext
│   └── caches/                  # Fulltext & abstract caches
├── 09_finalization/             # Final processing
└── 10_quality_control/          # QC fixes
```

---

## Key Files

| File | Location | Rows | Description |
|------|----------|------|-------------|
| `01_input_query_results.csv` | `00_input/` | 98,950 | Raw EPMC query |
| `01a_classification_union.csv` | `01_classification/` | 27,978 | Positive papers |
| `02a_spacy_ner_results.csv` | `02_ner/` | 68,151 | SpaCy NER entities |
| `03b_high_score_papers.csv` | `03_linguistic_scoring/` | 5,734 | High linguistic score |
| `04b_setfit_introductions.csv` | `04_setfit/` | 5,041 | Introduction papers |
| `07c_set_c_final.csv` | `07_deduplication/aggressive/` | 5,096 | Aggressive dedup |
| `09f_final_inventory.csv` | `09_finalization/` | 1,510 | Final inventory |
| `10f_final_inventory_qc_fixed.csv` | `10_quality_control/` | 1,510 | **QC-fixed** final |

---

## URL Recovery

| Stage | URLs | Success Rate |
|-------|------|--------------|
| Abstract extraction | 234,671 attempts | - |
| Fulltext extraction | 762,664 attempts | - |
| Web search | 1,037 results | - |
| **Total recovered** | 1,520 | 80.4% |
| Still missing | 94,361 | - |

---

## Caches

Large cache files for reproducibility:
- `08g_abstracts_cache.json` (4.2 MB) - Abstract text cache
- `08h_fulltext_cache.json` (107 MB) - Fulltext cache
