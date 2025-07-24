# CellChat Exploration – Stage 1

## Goal  
Use the **YAP‑CA single‑cell dataset** to  

1. catalogue ligand–receptor (LR) expression in  
   - cardiomyocytes (CMs)  
   - cardiac fibroblasts (CFs)  
2. flag CM‑secreted or trans‑membrane ligands that are **down‑regulated** in the **Tox3‑KO bulk RNA‑seq** data.

---

## Resources  
| Item | Link |
|------|------|
| Dataset | <https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE152856> |
| CellChat repository | <https://github.com/jinworks/CellChat> |

---

## Workflow Overview

1. **Download** processed counts + metadata from **GSE152856**  
   * 6 adult‑heart Drop‑seq libraries (3 control, 3 YAP5SA).  
   * Metadata already contains per‑cell cluster IDs & sample labels → no raw alignment needed.

2. **Re‑cluster** to distinguish CMs vs CFs  
   * Dataset is CM‑enriched; CFs are a minority.  
   * Run `FindClusters()` at higher resolution (e.g. `0.6`) to prevent CFs from being absorbed into CM clusters.  

   **Why resolution matters**  
   * high resolution: splits large clusters, isolates rarer cell types (CFs).  
   * Too high: over‑splits biologically coherent clusters.  
   * **Action plan:** test `0.4`, `0.6`, `0.8`; inspect marker genes (e.g. `Tnnt2`, `Col1a1`) to pick the cleanest separation.

3. **Down‑regulation screen**  
   * With CellChat, extract CM ligands (secreted / TM) and CF receptors.  
   * Intersect ligand list with genes down‑regulated in Tox3‑KO bulk RNA‑seq.

---

## Detailed Setup (Linux/macOS)

### Prerequisites  
* Command‑line essentials (`git`, `curl`, `wget`)  
* **Homebrew** package manager  
* **RStudio** (or any R IDE)  

### Install R

```bash
brew install --cask r
```
Verify:

```bash
R --vanilla -q -e 'R.version.string'
```
### Create an isolated R library
```bash
mkdir ~/cellchat_stage1 && cd ~/cellchat_stage1
R --vanilla
# Inside R:
install.packages("renv")
renv::init()  # sets up a project-specific library
q()
```
### Install the required R packages
Open R again and inside the same folder:
```bash
install.packages("BiocManager")
BiocManager::install(c("Seurat",         # scRNA‑seq processing
                       "CellChat",       
                       "GEOquery",       # download from GEO
                       "patchwork",      # figure 
                       "tidyverse",      # data wrangling
                       "data.table"))
BiocManager::install(c("zellkonverter", "scuttle"))
q()
```
### see the YAP/Control single-cell dataset
I loaded into this repo, you can find it there.

