# 🧬 BACE1 Inhibitor Discovery for Alzheimer's Disease

**Deep Learning + Molecular Docking pipeline to identify potential BACE1 inhibitors**

A hybrid AI + structure-based virtual screening approach combining Random Forest,
LSTM-NLP, and Deep Neural Networks with AutoDock Vina molecular docking validation.

--

## 🎯 Why BACE1?

β-Site Amyloid Precursor Protein Cleaving Enzyme 1 (**BACE1**) is the rate-limiting
enzyme in the formation of amyloid-β peptides — the toxic plaques characteristic
of Alzheimer's disease. Inhibiting BACE1 is one of the most pursued therapeutic
strategies in AD drug discovery.

---

## 📊 Dataset

| Stage | Compounds | Source |
|------|-----------|--------|
| Raw  | 15,264    | ChEMBL — BACE1 bioactivity database |
| Filtered (IC50, nM, exact, deduplicated) | 8,253 | — |
| Train | 6,601 | 80% split |
| Test  | 1,652 | 20% split |

**Features (2056 total)**
- 2048-bit Morgan fingerprints (ECFP) — structural patterns
- 8 RDKit descriptors — MolWt, LogP, HBD, HBA, TPSA, RotBonds, Rings, HeavyAtoms

---

## 🤖 Models Compared

| Model | Approach |
|-------|----------|
| Random Forest (RF) | Descriptor-based regression |
| LSTM-NLP | Tokenized SMILES sequence learning |
| **DNN (winner)** | 1024 → 512 → 256 → 128 → 1, ReLU, BatchNorm, Dropout |

**Best model:** Descriptor-based DNN — strong correlation between predicted and actual pIC50 on the 1,652-ligand test set.

---

## 🔬 Pipeline

```
ChEMBL (15,264) → Filter → 8,253 → Train/Test split
                                      ↓
                       RF · LSTM · DNN — DNN selected
                                      ↓
                          Predict pIC50 on test set
                                      ↓
                            Top 30 candidates
                                      ↓
                AutoDock Vina docking → binding affinity (ΔG)
                                      ↓
              Consensus score = z(pIC50) − z(ΔG)
                                      ↓
                       Final ranked candidates
```

See `docs/workflow_ACB.png` for the full diagram.

---

## 🏆 Top 3 Validated Hits

| ChEMBL ID | Binding Affinity (ΔG) | Key Interactions |
|-----------|----------------------|------------------|
| **CHEMBL4564324** | −11.9 kcal/mol | Conventional H-bonds with TYR132, TYR259, GLY95; Pi-Sulfur with TYR132 |
| **CHEMBL5286535** | −10.9 kcal/mol | H-bonds with THR293, GLY72, TYR132; Halogen interactions with SER71 |
| **CHEMBL3922952** | −9.0 kcal/mol | H-bonds with ASP93, GLY95; Pi-anion and Pi-Pi interactions |

See `images/` for 2D interaction maps and `docking/` for the docked PDB files.

---

## 📁 Repository Structure

```
.
├── notebooks/        Data prep, training, prediction, docking analysis
├── data/             Raw + processed BACE1 inhibitor data
├── models/           Saved models + preprocessing pipeline (.pkl)
├── results/          DNN predictions, top 30, predicted Ki values
├── docking/          5HE7 protein + docked top hits (PDB)
├── md_simulation/    GROMACS topology for top ligand (I626) — for future MD
├── images/           2D protein–ligand interaction diagrams
└── docs/             Report (PDF), presentation, workflow diagram
```

---

## 🚀 Quick Start

```bash
git clone https://github.com/pradip25215-sudo/bace1-alzheimer-drug-discovery.git
cd bace1-alzheimer-drug-discovery
pip install -r requirements.txt

# Run the full pipeline
jupyter notebook notebooks/02_full_pipeline.ipynb
```

---

## 🛠️ Tech Stack

Python · RDKit · TensorFlow / Keras · Scikit-learn · XGBoost · AutoDock Vina ·
BIOVIA Discovery Studio (interaction maps) · GROMACS (MD prep) · pandas · NumPy

---

## 🔭 Future Work

- [ ] Large-scale virtual screening (~2.5M compounds from ZINC)
- [ ] MD simulations of top hits (GROMACS, files in `md_simulation/`)
- [ ] MM-PBSA/MM-GBSA free energy calculations
- [ ] ADMET prediction
- [ ] Experimental validation collaboration

---

## 📚 References

- ChEMBL BACE1 bioactivity dataset
- AutoDock Vina (Trott & Olson, 2010)
- RDKit cheminformatics toolkit
- Alzheimer's disease drug discovery literature

---

## 👥 Authors
 **Pradip N. Palekar**
M.Tech Computational Biology, IIIT Delhi (2025–2027)

## 📄 License

MIT
