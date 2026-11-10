# HG008 Single-Cell Tree Inference

Data and analysis code for reconstructing a reference single-cell phylogeny of the HG008-T pancreatic ductal adenocarcinoma cell line using ScisTree2. The analysis starts from read counts for **7,844 mutation sites across 119 cells**, applies cell-level quality control, and reconstructs the reference phylogeny using the **101 retained cells**.

The repository includes the input count matrices, a Jupyter notebook documenting the analysis, and the selected binary genotype matrix compatible with a perfect phylogeny.

## Repository contents

| File | Description |
| --- | --- |
| [`ad_alt_per_cell_nist_variants.tsv`](ad_alt_per_cell_nist_variants.tsv) | Alternate-read counts for 7,844 mutation sites across 119 cells. |
| [`dp_per_cell_nist_variants.tsv`](dp_per_cell_nist_variants.tsv) | Total read depths for the same mutation sites and cells. |
| [`single-cell_tree_inference.ipynb`](single-cell_tree_inference.ipynb) | Analysis notebook: genotype calling for quality control, preliminary tree inference, cell filtering, final tree inference, and candidate scoring. |
| [`best_NLL_solution_conflict_free_matrix.tsv`](best_NLL_solution_conflict_free_matrix.tsv) | Selected inferred genotype matrix for 101 cells and 7,844 mutation sites. Entries are 0 (absent) or 1 (present). |

## Data source

The underlying single-cell whole-genome sequencing data are part of the NIST Genome in a Bottle HG008 resource described by [McDaniel et al. (2025)](https://doi.org/10.1038/s41597-025-05438-2). The single-cell libraries were prepared using BioSkryb ResolveDNA primary template-directed amplification and sequenced on the Ultima Genomics platform (dataset `sc-Ultima-1`).

The analysis in this repository begins with the supplied read-count matrices. In both input files, rows represent mutation sites, columns represent cells, and the `Mut` column contains mutation identifiers. The notebook transposes these matrices so that cells are rows and mutations are columns. It also shortens cell identifiers, for example from `NIST-PL01-SC19` to `C19`, and standardizes mutation identifiers, for example from `chr1_1886301_G_A` to `1-1886301-G-A`.

## Running the analysis

The notebook requires **Python, NumPy, pandas, ScisTree2, and Jupyter Notebook**. See the [official ScisTree2 repository](https://github.com/yufengwudcs/ScisTree2#installation) for installation details.

From the repository directory, install the packages and open the notebook:

```bash
python -m pip install numpy pandas scistree2 notebook
python -m notebook single-cell_tree_inference.ipynb
```

Keep both input count files in the same directory as the notebook. Start with a fresh kernel and run the analysis cells in order. The notebook documents the analysis settings and individual steps.

## Analysis summary

The notebook performs preliminary tree inference and quality control, excluding 17 cells with excessive putative false-positive counts and one cell with insufficient coverage. It then reconstructs trees for the 101 retained cells across 40 combinations of sequencing-error and allelic-dropout parameters, using eight threads per run. Candidates are scored using the genotype false-positive and false-negative rates estimated for each reconstruction.

## Final output

The highest-likelihood reconstruction among the evaluated candidates is encoded in [`best_NLL_solution_conflict_free_matrix.tsv`](best_NLL_solution_conflict_free_matrix.tsv) as a **conflict-free (perfect phylogeny) matrix**. Rows represent cells, columns represent mutation sites, and entries indicate mutation absence (`0`) or presence (`1`). A corresponding tree can be reconstructed from this matrix; see [Malikić et al. (2021)](https://doi.org/10.1089/cmb.2020.0595). Running the notebook regenerates this output file.

## References

- **HG008 data:** McDaniel, J. H. et al. (2025). [Development and extensive sequencing of a broadly-consented Genome in a Bottle matched tumor-normal pair](https://doi.org/10.1038/s41597-025-05438-2). *Scientific Data* **12**, 1195.
- **ScisTree2:** Zhang, H. et al. (2025). [ScisTree2 enables large-scale inference of cell lineage trees and genotype calling using efficient local search](https://doi.org/10.1101/gr.280542.125). *Genome Research* **35**(12), 2781–2791.
- **Matrix-based phylogeny reconstruction:** Malikić, S. et al. (2021). [Studying the History of Tumor Evolution from Single-Cell Sequencing Data by Exploring the Space of Binary Matrices](https://doi.org/10.1089/cmb.2020.0595). *Journal of Computational Biology* **28**(9), 857–879.
