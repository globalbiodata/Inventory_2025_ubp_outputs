# Bioresource Discovery Pipeline Outputs (2025)

**Repository**: Archival output files from the Unified Bioresource Pipeline (UBP)
**Generated**: December 2025
**Source Pipeline**: `inventory_2022` repository

---

## Overview

This repository contains the curated output files from the bioresource discovery ML pipeline, which processes scientific literature to identify new bioinformatics databases and resources.

### Final Results

| Output | Count | Description |
|--------|-------|-------------|
| **Novel Resources** | 1,370 | New bioresources not in baseline inventory |
| **Baseline Matches** | 825 | Resources matching existing inventory |
| **Total Processed** | 248,514 papers | From EPMC V5.1 query (2011-mid2025) |

---

## Directory Structure

```
Inventory_2025_ubp_outputs/
├── 00_baseline_references/     # Papers/entities matching existing inventory
├── 01_batch_2011_2021/         # First batch (149,943 papers)
├── 02_batch_2022_mid2025/      # Second batch (98,571 papers)
├── 03_final_merged/            # Combined and deduplicated outputs
└── 04_documentation/           # Data dictionaries and guides
```

---

## Processing Batches

### Batch 1: 2011-2021 (149,943 papers)
- **Input**: EPMC V5.1 query results
- **Classification**: V2 RoBERTa + PyCaret ensemble
- **NER**: V2 BERT + SpaCy Hybrid
- **Final outputs**: 964-10,822 resources (by dedup profile)

### Batch 2: 2022-mid2025 (98,571 papers)
- **Input**: EPMC V5.1 query results
- **Classification**: V2 RoBERTa + PyCaret ensemble
- **NER**: SpaCy (68K entities) + V2 BERT (35K entities)
- **Final outputs**: 1,510 resources with validated URLs

### Final Merged
- **Combined**: 2,506 resources from both batches
- **After dedup**: 2,422 unique resources
- **After baseline filtering**: 1,597 novel resources
- **Final filtered**: 1,370 high-quality novel bioresources

---

## Key Output Files

| File | Location | Rows | Description |
|------|----------|------|-------------|
| `05a_final_filtered.csv` | `03_final_merged/05_final_output/` | 1,370 | **FINAL** - Novel bioresources |
| `03a_confirmed_baseline_matches.csv` | `00_baseline_references/` | 825 | Confirmed baseline matches |
| `10f_final_inventory_qc_fixed.csv` | `02_batch_2022_mid2025/10_quality_control/` | 1,510 | QC-fixed 2022-mid2025 inventory |
| `08d_linguistic_high_conf_final.csv` | `01_batch_2011_2021/08_final_outputs/` | 964 | High-confidence 2011-2021 resources |

---

## Pipeline Flow

```
248,514 papers (EPMC V5.1)
    ↓ Phase 1: Classification (V2 RoBERTa + PyCaret)
    ↓ Phase 2: NER (V2 BERT + SpaCy Hybrid)
    ↓ Phase 3: Linguistic Scoring
    ↓ Phase 4: SetFit Classification
    ↓ Phase 5: Entity Mapping
    ↓ Phase 6: URL Scanning
    ↓ Phase 7: Deduplication (3 profiles)
    ↓ Phase 8: URL Recovery
    ↓ Phase 9: Finalization
    ↓ Cross-batch deduplication
    ↓ Baseline filtering
    → 1,370 novel bioresources
```

---

## Baseline Reference Files

Papers and entities that match the existing baseline inventory are stored separately in `00_baseline_references/`. These can be used to:
- Update existing inventory entries with new information
- Track citations to known resources
- Identify papers for literature reviews

See `00_baseline_references/README.md` for details.

---

## Deduplication Profiles

Three deduplication stringency levels are available:

| Profile | Strategy | 2011-2021 | 2022-mid2025 |
|---------|----------|-----------|--------------|
| **Aggressive** | Max recall | 10,822 | 5,096 |
| **Balanced** | Recommended | 5,979 | 1,708 |
| **Conservative** | Max precision | 4,172 | 1,163 |

---

## Data Storage

This repository uses Git LFS for large files (>50MB). Key large files include:
- Input query results (~284 MB, ~158 MB)
- Classification results (~165 MB each)
- NER results (~129 MB)
- Fulltext cache (~107 MB)

---

## Source Pipeline

The pipeline code that generated these outputs is in the `inventory_2022` repository:
- Pipeline orchestrator: `unified_bioresource_pipeline/run_pipeline.py`
- Configuration: `unified_bioresource_pipeline/config/pipeline_config.yaml`
- Documentation: `docs/starting_doc.md`

---

## Contact

For questions about this data, contact the Global Biodata Coalition.
