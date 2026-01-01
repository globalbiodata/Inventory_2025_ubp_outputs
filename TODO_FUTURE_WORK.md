# Future Work and Technical Debt

This document outlines work that was not completed as part of the 2025 inventory update, along with known limitations and areas for future improvement.

---

## 1. Baseline Inventory Maintenance

### 1.1 What Wasn't Updated

The 2025 pipeline run focused on discovering **novel bioresources** - databases and tools not already present in the existing inventory. The baseline inventory (3,112 resources) was used as a filter to exclude known resources from the final output, but **the baseline entries themselves were not updated**.

This means for old resources:
- Existing database URLs may now be broken or redirected
- Resource names may have changed (mergers, rebranding)
- Metadata (descriptions, affiliations) may be outdated
- New papers citing these resources were not linked back to baseline entries

### 1.2 Baseline Reference Files

During processing, we identified papers and entities that matched baseline resources. These files are preserved in `00_baseline_references/` and can be used to update the baseline inventory:

| File | Records | Purpose |
|------|---------|---------|
| `03a_confirmed_baseline_matches.csv` | 825 | High-confidence matches ready for review |
| `03b_baseline_high_confidence.csv` | 578 | Subset with strongest match signals |
| `03c_baseline_fuzzy_review.csv` | 192 | Fuzzy name matches requiring manual verification |
| `03d_baseline_url_matches.csv` | 312 | Matches identified by URL similarity |

These files contain updated URLs, new paper citations, and metadata that could refresh baseline entries.

### 1.3 Inactive Websites

Many baseline databases likely have websites that are no longer active. A systematic check is needed to:

1. **Identify dead URLs** - Resources where the primary website returns 404, times out, or has been parked
2. **Distinguish truly dead vs relocated** - Some may have moved to new URLs without redirects
3. **Note archive-only resources** - Presence on archive.org or Wayback Machine does not count as "active" for inventory purposes
4. **Flag for removal or archival status** - Decide whether to mark resources as deprecated or remove entirely

### 1.4 Entity Updates: Name and URL Changes

Databases evolve over time - they merge, rebrand, change institutions, and update URLs. The baseline inventory needs to track these changes carefully to avoid creating duplicate entries.

**Example: IUPHAR-DB → Guide to PHARMACOLOGY**

IUPHAR-DB (International Union of Basic and Clinical Pharmacology Database) underwent a significant transition in 2011-2014. It merged with the British Pharmacological Society's resources and was rebranded as the "IUPHAR/BPS Guide to PHARMACOLOGY" (commonly abbreviated GtoPdb). The URL changed from `www.iuphar-db.org` to `www.guidetopharmacology.org`, with the old URL now redirecting to the new site.

This creates a data quality challenge: papers published before 2012 reference "IUPHAR-DB" while newer papers reference "Guide to PHARMACOLOGY" - but they describe the same resource. Without careful entity resolution, these could appear as two separate database entries in the inventory.

**When updating baseline entities, each resource should be checked for:**
- Name changes or rebranding
- URL redirects or domain changes
- Mergers with other resources
- Potential duplicate entries under old and new names

---

## 2. Pipeline Consolidation

### 2.1 Current State

The unified bioresource pipeline currently consists of 27+ scripts spread across multiple directories. While functional, the architecture has accumulated complexity:

- Scripts reference 4+ different base directories, causing path inconsistencies
- Some processing steps are split across multiple scripts that could be combined
- External dependencies (Google Colab for SetFit) break the local execution flow
- Similar logic is duplicated across scripts rather than shared

### 2.2 Consolidation Opportunities

The following script groups could be simplified:

**Classification Pipeline**
Currently uses separate scripts for V2 RoBERTa classification and PyCaret metadata classification, then a third script to merge results. These could be consolidated into a single classification script that:
- Runs both classifiers in sequence
- Produces a unified output with confidence scores from both models
- Handles the union logic internally

**NER Pipeline**
SpaCy hybrid NER and V2 BERT NER run separately, then results are merged. A unified NER script could:
- Run both models on the same input
- Perform entity-level deduplication immediately
- Output a single NER results file with source attribution

**Paper Scoring**
Linguistic scoring (keyword-based) and SetFit classification (ML-based) both identify "introduction" papers. These complementary approaches could be:
- Combined into a single paper scoring script
- SetFit integrated locally rather than requiring Colab
- Output unified paper sets (A, B, C) in one pass

**Deduplication**
Three deduplication profiles (aggressive, balanced, conservative) run as separate passes. A parameterized script could:
- Accept profile as command-line argument
- Run all three profiles in sequence if requested
- Share common deduplication logic

**URL Resolution**
URL extraction from abstracts, fulltext URL recovery, and web search recovery are separate scripts. A unified URL resolution script could:
- Attempt all recovery methods in sequence
- Track which method succeeded for each URL
- Reduce redundant text processing

### 2.3 Benefits of Consolidation

- **Fewer scripts to maintain** - Reduced cognitive load and documentation needs
- **Cleaner data flow** - Intermediate files stay within script boundaries
- **Easier debugging** - Issues localized to specific consolidated scripts
- **Simpler execution** - Fewer commands to run the full pipeline

---

## 3. Model Improvements

### 3.1 Consolidated BERT + Metadata Model

Early in development, we investigated whether a single model could handle both text classification and metadata features together. The idea was to embed structured metadata (publication year, journal type, MeSH terms, etc.) directly into the RoBERTa transformer architecture alongside the text input.

This approach proved less reliable than keeping the models separate:
- The metadata signal was diluted when mixed with text embeddings
- Training was less stable, with higher variance across runs
- The separate PyCaret metadata classifier achieved better recall (84.6%) than the integrated approach

The current production architecture uses V2 RoBERTa for text classification and PyCaret for metadata classification, with results merged via union. While less elegant than a single unified model, this approach delivers better performance.

**Future consideration**: This approach should be revisited as due to time constraints creating an integrated model was not possible.

### 3.2 PyCaret Metadata Classifier

The metadata-only classifier currently achieves 84.6% recall on validation data. Analysis of missed papers shows opportunities for improvement:
- Threshold optimization could potentially reach 92.3% recall
- Two missed papers had incomplete metadata (hasData=0%, unknown journals)
- Stacking ensemble methods may outperform current blending approach

### 3.3 SpaCy Hybrid NER Recall

The SpaCy hybrid NER model shows 91% precision but only 48% recall in validation. However, this validation used titles only - abstracts would significantly improve recall. Future work should:
- Re-run validation with full abstracts
- Expand EntityRuler patterns for commonly missed resources
- Add full-name patterns for abbreviations

---

## 4. Test Set Methodology

### 4.1 The Problem

A critical finding from early development: **how you define the test set dramatically impacts reported model accuracy**. This is not a bug - it's a fundamental issue with how NER models are evaluated on datasets with variable entity complexity.

### 4.2 Entity Complexity Impact

Our NER task extracts two entity types:
- **Short names** (e.g., "UniProt", "PDB") - typically 1-2 words, easy to detect
- **Full names** (e.g., "Protein Data Bank", "Mouse Phenome Database") - variable length, harder to detect

When we systematically varied the proportion of complex (>3 word) entities in test sets, we observed:

| Test Set Complexity | Measured F1 |
|--------------------|-------------|
| 5% complex entities | ~0.75+ |
| 17% complex entities | ~0.72 |
| 25% complex entities | ~0.68 |
| 50% complex entities | ~0.60 |

**This 15 percentage point swing is not model performance - it's test set composition.**

A model reporting F1=0.75 on an "easy" split and another reporting F1=0.65 on a "hard" split may have identical actual performance. Without controlling for entity complexity, F1 comparisons between models or runs are unreliable.

### 4.3 Current Status

Phase 1 experiments confirmed this hypothesis:
- Split D (17% complexity) achieved F1=0.7165
- This closed 69% of the gap vs the V2 baseline (F1=0.749)
- The remaining gap is likely due to other split differences, not model architecture

### 4.4 Future Work Required

1. **Standardize complexity reporting** - Any published F1 score should include the test set's entity complexity distribution
2. **Stratified splitting** - Future train/test splits should stratify by entity length to ensure comparable distributions
3. **Multiple test sets** - Report performance across easy, medium, and hard test sets for complete picture
4. **Reproduce V2 baseline conditions** - Determine the exact complexity distribution of the original V2 test set

This investigation is documented in detail in the source repository under `docs/PHASE2_ENTITY_COMPLEXITY_EXPERIMENT.md`.

---

## 5. Additional Technical Debt

### 5.1 Unit Test Coverage

The codebase lacks systematic unit test coverage. Key areas that would benefit from pytest-based testing include:

- **Data splitter edge cases** - Empty inputs, single samples, unbalanced classes, boundary conditions
- **NER post-processing** - Various entity boundary conditions, multi-word entity handling, overlapping entities
- **Batch processing** - Edge sizes (1, 8, 32, 64, 128 samples), memory handling with large batches
- **Pipeline component validation** - Missing inputs, malformed data, unexpected column names
- **Empty DataFrame handling** - Graceful handling of empty inputs throughout the pipeline

The SpaCy hybrid NER code has been manually tested extensively but would benefit from automated regression tests to catch future regressions during refactoring.

---

## 6. Operational Improvements

### 6.1 Session-Based Architecture

The pipeline now uses session-based directory structures (timestamped folders for each run). This improves reproducibility but creates cleanup overhead. Automated purging of sessions older than 30 days would reduce storage accumulation.

### 6.2 URL Validation Schedule

The URL scanner identified 81.5% of resources with live URLs at time of processing. URLs decay over time - a quarterly re-scan would catch newly broken links and maintain data quality.

### 6.3 EPMC Query Maintenance

The current query (V5.1) achieves 77.3% capture of training positives. New resource terminology emerges over time. An annual review of query precision and recall against known resources would catch drift early.

