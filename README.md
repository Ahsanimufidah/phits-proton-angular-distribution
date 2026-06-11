# Proton Multiple Coulomb Scattering Analysis — PHITS Phase-Space Post-Processing

This repository contains a Python-based post-processing workflow for quantifying multiple Coulomb scattering (MCS) of protons using phase-space output from PHITS Monte Carlo simulations.

The analysis is part of a broader study examining how layered phantom materials affect proton beam transport in tissue-equivalent media. This repository focuses on a water–bone–water phantom configuration, which serves as a bone-only reference case for evaluating angular broadening before comparing with implant-containing configurations.

## Physical Background

When a proton beam traverses a material, Coulomb interactions with atomic nuclei cause small random deflections that accumulate into a broadened angular distribution. This phenomenon is known as multiple Coulomb scattering.

Quantifying multiple Coulomb scattering is important in proton therapy because additional beam scattering from dense materials or anatomical heterogeneities can affect beam directionality and potentially influence dose conformity near the target region.

This analysis estimates the multiple-scattering angle θ₀, defined as the Gaussian width of the angular distribution, at several scoring planes along a 140 MeV proton beam. Per-layer scattering contributions are estimated using successive variance subtraction between adjacent scoring planes:

$$
\theta^2_{0,\text{layer}} = \theta^2_{0,\text{after}} - \theta^2_{0,\text{before}}
$$

## Simulation Setup

| Parameter        | Value                               |
| ---------------- | ----------------------------------- |
| Monte Carlo code | PHITS v3.34                         |
| Beam energy      | 140 MeV                             |
| Beam type        | Monoenergetic, parallel proton beam |
| Field size       | 5 × 5 cm²                           |
| Phantom geometry | Water–Bone–Water                    |
| Phantom size     | 30 × 30 × 30 cm³                    |
| Bone composition | Cortical bone, ICRP       |
| Bone density     | ρ = 1.85 g/cm³                      |
| Scoring planes   | 7 scoring planes                    |
| Raw file size    | Approximately 27 GB per plane       |

## Analysis Workflow

Raw PHITS phase-space files contain particle position, direction cosines, energy, and statistical weight for protons crossing each scoring plane. The post-processing workflow proceeds as follows:

1. Parse PHITS dump files in chunks to reduce memory usage.

2. Retain proton records using the PHITS particle identifier `kf = 2212`.

3. Compute the polar scattering angle using the beam-axis direction cosine:

   $$
   \theta = \arccos(w)
   $$

4. Build angular histograms from the extracted phase-space data.

5. Apply annular weighting to account for angular bin geometry.

6. Cache the processed histograms as `.npz` files to avoid repeatedly parsing large raw PHITS outputs.

7. Fit the core angular distribution using a Gaussian multiple-scattering model.

8. Estimate cumulative and per-layer scattering widths.

9. Export figures and tabular summaries for review and documentation.

## Repository Structure

```text
phits-proton-angular-distribution/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── inputs/
│   └── simulation_input.inp
│
├── notebooks/
│   └── Scattering_PSFB.ipynb
│
├── processed/
│   └── histograms.npz
│
└── results/
    ├── mcs_distributions.png
    ├── mcs_summary.png
    └── mcs_results.csv
```

## Data Availability

The original PHITS phase-space output files are not included in this repository because of their large file size. Each raw phase-space output can reach tens of gigabytes, making it unsuitable for direct upload to GitHub.

To keep the repository lightweight and reviewable, this repository provides:

* `inputs/simulation_input.inp` — PHITS input file used for the simulation setup.
* `processed/histograms.npz` — cached angular histogram data derived from the raw PHITS output.
* `results/` — exported figures and CSV summary from the analysis.
* `notebooks/Scattering_PSFB.ipynb` — full analysis notebook.

Users who want to reproduce the analysis from raw PHITS output should place their own `.out` dump files in a local project folder and update the `PROJECT_ROOT` variable in the notebook configuration cell.

## Key Outputs

The repository includes the following result files:

* `results/mcs_distributions.png`
  Angular distribution plots with Gaussian fitting results.

* `results/mcs_summary.png`
  Summary visualization of the estimated multiple-scattering parameters.

* `results/mcs_results.csv`
  Tabular summary of the estimated scattering widths.

## How to Run

Clone the repository:

```bash
git clone https://github.com/Ahsanimufidah/phits-proton-angular-distribution.git
cd phits-proton-angular-distribution
```

Install the required Python packages:

```bash
pip install -r requirements.txt
```

Open the notebook:

```text
notebooks/Scattering_PSFB.ipynb
```

There are two ways to use the notebook:

### Option 1 — Use the cached histogram file

This is the recommended option for reviewing the analysis without reprocessing large PHITS output files.

Run the notebook cells that load:

```text
processed/histograms.npz
```

### Option 2 — Reprocess raw PHITS output

To reproduce the workflow from raw PHITS phase-space files:

1. Place the `.out` files in a local project folder.
2. Update the `PROJECT_ROOT` variable in the notebook.
3. Run the parsing, histogram-building, fitting, and export cells.

## Requirements

The analysis uses the following Python libraries:

```text
numpy
pandas
matplotlib
scipy
```

These packages can be installed using:

```bash
pip install -r requirements.txt
```

## Notes

This repository is intended as a research and portfolio project demonstrating:

* Monte Carlo simulation post-processing,
* PHITS phase-space data handling,
* proton angular distribution analysis,
* multiple Coulomb scattering estimation,
* histogram-based processing for large simulation outputs,
* scientific visualization using Python.

The raw simulation output files are excluded to keep the repository lightweight. The processed histogram cache and exported results are included so the analysis can still be reviewed without requiring access to the full raw PHITS output.

## Author

**Ahsani Mufidah**
GitHub: [@Ahsanimufidah](https://github.com/Ahsanimufidah)

M.Sc. in Physics
Institut Teknologi Bandung (ITB)
