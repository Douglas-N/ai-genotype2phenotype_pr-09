# Genotype2Phenotype — Linking Mutations, Expression and Drug Response with Interpretable AI

## Pitch
This project integrates RNA-seq data, somatic mutations, and cell viability assays (p53-REACT and collaborators) to:  
1. Identify differentially expressed genes (tumor vs normal).  
2. Detect pathogenic mutations with functional impact.  
3. Predict compound response using interpretable multimodal AI models.  

The final deployment is an API + Gradio demo with reproducible Docker/SageMaker setup and exploratory notebooks.

---

## Motivation & Relevance
Translating genomic variants and expression signatures into clinical predictions (e.g., drug response) is crucial for translational medicine.  
Multimodal modeling (mutations + expression + in vitro assays) can prioritize pathogenic variants, suggest mechanisms, and guide experimental decisions.

---

## Data Sources
- **Proprietary (collaboration):** p53-REACT cell viability assays. Used as held-out real-world evidence.  
- **Public datasets:**  
  - RNA-seq: TCGA or GEO (tumor-specific datasets). Preprocessed counts/TPM preferred.  
  - Somatic variants: TCGA MAF, DepMap/CCLE.  
  - Drug sensitivity: DepMap PRISM/GDSC.  
  - Optional: Hugging Face datasets for processed RNA-seq or bio/seq data.

---

## Objectives
1. **Differential Expression Analysis:**  
   - Identify DE genes (log2FC, p-value, adj. p).  
   - Visualizations: volcano plots, heatmaps.  

2. **Mutation Classification:**  
   - Train model to classify variants as pathogenic vs benign using variant-level features, structural context (AlphaFold), and expression.  
   - Output: probability of pathogenicity, SHAP feature interpretation.  

3. **Drug Response Prediction:**  
   - Multimodal model: mutation vector + expression vector + compound fingerprint/SMILES embedding → predicted response.  
   - Output: predicted sensitivity/resistance or IC50; explainable via SHAP/attention.

---

## Technical Approach

### A. Data Ingestion & Preprocessing
- Scripts: ,   
- Steps:
  - Normalize expression (TPM/CPM, log1p).  
  - Binarize mutations (gene-level or variant-level).  
  - Compound processing: fingerprints (RDKit) or SMILES embeddings (ChemBERTa).  
  - Train/val/test split per study or cell line to avoid leakage.

### B. EDA & Bioanalysis
- Notebook:   
- Tasks:
  - Distribution of expression, PCA/UMAP, clustering.  
  - Volcano plots, pathway enrichment (gseapy).  
  - Correlate mutations with DE genes.  
  - Boxplots/dose-response for compounds.

### C. Multimodal Modeling
- Code:   
- Architecture example:
  - Branch A: Expression → Autoencoder/1D-CNN → embedding  
  - Branch B: Mutations → dense layers/graph → embedding  
  - Branch C: Compound → fingerprint/SMILES embedding → embedding  
  - Fusion: concat embeddings → MLP → output (Softmax/linear)  
- Framework: PyTorch  
- Metrics: ROC-AUC, PR-AUC (classification), RMSE/R2 (regression)  
- Regularization: dropout, early stopping, weighted loss

### D. Interpretability
- SHAP for tabular features (gene/mutation importance).  
- Integrated gradients / attention for sequence/transformer parts.  
- Example output: top genes/mutations driving prediction.

### E. Testing & Reproducibility
- Unit tests: ,  (pytest)  
- Integration test: small synthetic dataset end-to-end  
- Reproducible: deterministic seeds, pinned requirements

### F. Deployment
- SageMaker: PyTorchModel endpoint  
- Lightweight: Docker + FastAPI + AWS Lambda  
- Demo UI: Gradio app (sequence + mutation + compound → prediction + SHAP)

---

## Quick Start

### Install Dependencies
```bash
pip install -r requirements.txt
```

### Train Local Model
```bash
python src/train.py --config configs/exp1.yaml
```

### Evaluate Model
```bash
python src/evaluate.py
```

### Run Gradio Demo
```bash
python deployment/gradio_app/gradio_app.py
```

### Docker & SageMaker
```bash
docker build -t genotype2phenotype .
# SageMaker: upload model.pt to S3, create PyTorchModel, deploy endpoint
```

---

## Expected Metrics
- DE analysis: adj. p < 0.05; top pathways: p53/apoptosis.  
- Mutation classifier: ROC-AUC ≥ 0.80  
- Drug response: PR-AUC/ROC-AUC 0.7–0.85  
- Interpretability: SHAP identifies biologically relevant genes/mutations.  

---

## Notebooks & Examples
- : volcano, heatmap, UMAP  
- : train baseline + multimodal models  

---

## Ethics & Data
- Documented source: GEO/TCGA/DepMap + collaboratiors  
- Clinical data anonymized  
- Public data scripts:   
- Proprietary data:  for collaborators

---

## CI/CD
- GitHub Actions workflow:   
- Runs: pytest, linting, notebook tests (nbconvert optional)  
- Only merge PRs with passing tests

---

## License
MIT
