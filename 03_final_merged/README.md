# Final Merged Batch

Combined and deduplicated outputs from both processing batches.

---

## Summary Statistics

| Metric | Value |
|--------|-------|
| Batch 1 input (2011-2021) | 996 resources with URLs |
| Batch 2 input (2022-mid2025) | 1,510 resources with URLs |
| Combined total | 2,506 |
| After cross-batch dedup | 2,422 |
| Novel (after baseline filtering) | 1,597 |
| **Final filtered** | **1,370** |

---

## Directory Structure

```
03_final_merged/
├── 00_input/                    # Per-batch inputs
├── 01_combined/                 # Combined batches
├── 02_cross_batch_dedup/        # Cross-batch deduplication
├── 03_baseline_filtering/       # Novel resources only
├── 04_naming_review/            # Name quality review
└── 05_final_output/             # FINAL OUTPUT
```

---

## Key Files

| File | Location | Rows | Description |
|------|----------|------|-------------|
| `01a_batch_2011_2021_live.csv` | `00_input/` | 996 | Batch 1 with live URLs |
| `01b_batch_2022_2025_live.csv` | `00_input/` | 1,510 | Batch 2 with live URLs |
| `01a_combined_batches.csv` | `01_combined/` | 2,506 | All batches combined |
| `02a_merged_inventory.csv` | `02_cross_batch_dedup/` | 2,422 | Cross-batch deduped |
| `03a_novel_resources_to_investigate.csv` | `03_baseline_filtering/` | 1,597 | Novel resources |
| `05a_final_filtered.csv` | `05_final_output/` | **1,370** | **FINAL OUTPUT** |

---

## Processing Flow

```
Batch 1: 996 resources  ──┐
                          ├─→ Combined: 2,506
Batch 2: 1,510 resources ─┘
                              ↓
                        Cross-batch dedup: 2,422 (-84 duplicates)
                              ↓
                        Baseline filtering: 1,597 novel (-825 baseline matches)
                              ↓
                        Naming review + QC: 1,370 final (-227 quality issues)
```

---

## Naming Review

Resources were reviewed in 8 chunks for name quality:
- `04a_chunk_001_results.csv` through `04h_chunk_008_results.csv`
- `04i_all_naming_results_merged.csv` - All review results
- `04j_new_resources_with_naming_review.csv` - Resources with review annotations

---

## Final Output

**`05a_final_filtered.csv`** (1,370 rows) contains:
- `best_name` - Resource name
- `extracted_url` - Resource URL
- `publication_date` - First publication
- `article_count` - Number of citing papers
- `source_batch` - Which batch (2011-2021 or 2022-mid2025)
- Quality indicators and confidence scores
