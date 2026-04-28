# Breast Cancer Gene Expression Analysis

## Project Overview

This project analyzes gene expression data from breast cancer patients to:
1. Classify tumors by molecular subtype (Luminal A vs Triple-Negative)
2. Identify genes that differ between subtypes
3. Visualize differential expression with publication-quality plots

**Why this matters:** Breast cancer is not one disease. Different molecular subtypes respond to different treatments. Understanding which genes are active in each subtype helps identify:
- Drug targets for therapy
- Prognostic markers for patient outcomes
- New research directions for aggressive cancers

---

## The Dataset

| Property | Value |
|----------|-------|
| Source | GEO (Gene Expression Omnibus) - Study EORTC 10994 |
| Platform | Affymetrix Human Genome U133A 2.0 |
| Samples | 49 breast cancer patients |
| Genes | ~19,000 (after mapping and cleaning) |

### Sample Classification
- **Luminal A (ER+)**: 6 samples (ERBB2 negative, ESR1 high)
- **Triple-Negative (ER-)**: 43 samples (ERBB2 negative, ESR1 low)

### Why These Samples?
The dataset contains samples from a clinical trial focusing on ER-negative and HER2-negative breast cancers. This makes it particularly interesting because Triple-Negative Breast Cancer (TNBC):
- Has no targeted therapies (no hormone therapy, no Herceptin)
- Relies solely on chemotherapy
- Has worse prognosis than other subtypes
- Is an active area of research for new treatments

---

## Methods

### Step 1: Data Collection
```
Raw data source: GEO (Gene Expression Omnibus)
Format: Series Matrix file (GPL platform)

Challenges:
- Data comes as probe IDs (technical identifiers), not gene symbols
- Need annotation file to map probe ID → gene name
- Some probe sets don't map to known genes (filtered out)
```

### Step 2: Data Preprocessing
```
1. Loaded expression matrix (49 samples × 22,215 probes)
2. Loaded annotation file (probe ID → gene symbol mapping)
3. Created mapping dictionary
4. Applied mapping to expression data
5. Removed unmapped probes
6. Final dataset: 19,050 genes × 49 samples

Note: Data was already log2-transformed, so no additional normalization was needed.
Log2 transformation compresses the range and makes data more normally distributed.
```

### Step 3: Quality Control
```
Checks performed:
- Missing values: 0 (no imputation needed)
- Data range: 4.00 to 14.27 (normal for log2 expression)
- Outlier detection: 1 sample (GSM26908) showed minor deviation but kept in analysis

Result: Data quality is good. All 49 samples included in analysis.
```

### Step 4: Molecular Subtype Classification
```
Classification based on established breast cancer markers:

| Marker | Subtype Indicator |
|--------|------------------|
| ESR1 (Estrogen Receptor) | High = Luminal, Low = ER- |
| ERBB2 (HER2) | High = HER2+, Low = HER2- |
| MKI67 (Ki-67) | Proliferation marker |

Thresholds used:
- ESR1 > 8 → ER positive
- ERBB2 > 10 → HER2 positive

Result: 6 Luminal A, 43 Triple-Negative samples identified.
```

### Step 5: Differential Expression Analysis
```
Comparison: Luminal A vs Triple-Negative

Method: Two-sample t-test (one per gene)

For each gene:
1. Calculate mean expression in Luminal A samples
2. Calculate mean expression in TNBC samples
3. Calculate fold change (log2 difference)
4. Calculate p-value (statistical significance)

Significance threshold: p < 0.05

Result: Identified genes with significantly different expression between subtypes.
```

### Step 6: Visualization
```
1. Volcano Plot
   - X-axis: Log2 fold change (difference between groups)
   - Y-axis: -log10(p-value) (statistical significance)
   - Red: Significantly higher in Luminal A
   - Blue: Significantly higher in TNBC
   - Gray: Not significant

Purpose: Quickly identify the most interesting genes (top-left and top-right corners)
```
## Key Findings

### Molecular Subtype Distribution
- **Luminal A**: 12.2% (6/49 samples) - ER positive, HER2 negative
- **Triple-Negative**: 87.8% (43/49 samples) - ER negative, HER2 negative

### Differentially Expressed Genes
- Total genes analyzed: 19,050
- Significantly different (p < 0.05): ~2 highly significant genes visible on volcano plot
- Genes upregulated in Luminal A: related to estrogen response
- Genes upregulated in TNBC: related to proliferation and basal markers

### Clinical Implications
1. Most samples in this dataset are TNBC, which lacks targeted therapies
2. Spatial transcriptomics approaches (like those used at SOMa Lab) could help identify:
   - New therapeutic targets
   - Tumor microenvironment features
   - Cell populations that might be vulnerable to treatment


## How to Run

### Prerequisites
```bash
pip install pandas numpy matplotlib scipy
```

### Run the Analysis
```bash
python breast_cancer_analysis.py
```

### Output
- Console output showing each step
- `volcano_plot.png` in the current directory
- `differential_expression_results.csv` with all gene statistics

---

## Technical Details

### Data Format
The raw Affymetrix data comes as:
```
Probe Set ID → Gene Symbol mapping
202619_s_at → PLOD2
219952_s_at → MCOLN1
205192_at   → MAP3K14
```

After mapping, the expression matrix contains:
```
          Sample1  Sample2  Sample3
DDR1      11.52    10.37    11.39    (log2 expression values)
RFC2       7.77     8.14     7.61
...
```

### Log2 Expression Values
- Why log2? Makes the data more normally distributed and compresses the range
- Interpretation: A value of 10 means ~1024 molecules detected
- A difference of 1 means ~2x difference in expression

---

## Connection to Spatial Transcriptomics

While this analysis uses bulk gene expression (average across all cells), the methods learned here apply to more advanced techniques:

1. **Single-cell RNA-seq**: Measures gene expression in individual cells
2. **Spatial transcriptomics**: Measures gene expression WITH location information
3. **SOMa Lab focus**: Using spatial transcriptomics to understand tumor architecture

The skills developed:
- Data preprocessing and QC
- Statistical analysis (t-tests, fold change)
- Visualization (volcano plots, heatmaps)
- Biological interpretation

These directly transfer to spatial transcriptomics analysis.

---

## Learning Outcomes

Through this project, I learned:
- How gene expression data is structured and stored
- How to map technical probe IDs to biological gene symbols
- How to perform differential expression analysis
- How to interpret results in the context of cancer biology
- How to create publication-quality visualizations
- How to use Git for version control

---

## Future Directions

1. **Gene Ontology Enrichment**: Identify which biological pathways are affected
2. **Heatmap Visualization**: Show expression patterns across all top genes
3. **Pathway Analysis**: Use KEGG/Reactome databases to find affected pathways
4. **Machine Learning**: Build classifiers to predict subtype from gene expression
5. **Spatial Integration**: Apply these concepts to spatial transcriptomics data
