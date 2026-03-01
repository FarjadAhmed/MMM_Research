### [Read it on Medium!](https://medium.com/@bluemacaw/can-a-top-down-hierarchical-mmm-recover-ground-truth-a-simulation-study-1537c0927cbe)

# Cascading Hierarchical MMM: Simulated Truth vs Top-Down Attribution

Can a cascading top-down Marketing Mix Model recover leaf-level channel contributions that were generated bottom-up? This repo tests that question with a fully simulated dataset where every parameter is known.

## The Experiment

Seven leaf channels are organized into three parent groups:

```
total_activations (Level 0 MMM)
├── digital_spend
│   ├── paid_search_spend
│   ├── paid_social_spend
│   └── display_spend
├── traditional_spend
│   ├── tv_linear_spend
│   └── direct_mail_spend
└── other_spend
    ├── affiliate_spend
    └── influencer_spend
```

**Bottom-up simulation (ground truth):** Spend is generated at the leaf level with known adstock + saturation parameters. Leaf contributions sum up to parent groups, then to total activations.

**Top-down cascading MMM:** Level 0 decomposes total activations into parent-channel contributions. Level 1 takes each parent's estimated contribution and attributes it to individual leaf channels.

## Key Findings

- **Total marketing contribution** was off by just 0.8% — the model knows how much marketing does in aggregate
- **Parent-level estimates** ranged from 9.5% to 28.2% error — directionally useful but not precise
- **Leaf-level attribution** showed 0–57% total error — temporal patterns tracked well (correlations > 0.89) but magnitudes were systematically distorted
- **Dominant channels lose share** to their siblings — the cascade flattens within-group differences
- **Parameter recovery was poor** — adstock alphas collapsed within groups, betas were systematically underestimated

The core issue: `sat(sum(spend)) ≠ sum(sat(spend))`. Level 0 uses one transformation on aggregated spend to approximate heterogeneous leaf effects — an approximation that breaks down when sibling channels have different adstock/saturation profiles.

## Results Summary

| Channel | True Total | Estimated Total | Error | Correlation |
|---|---|---|---|---|
| paid_search | 190.2 | 115.0 | -39.5% | 0.993 |
| paid_social | 102.6 | 102.7 | +0.0% | 0.980 |
| display | 31.6 | 48.8 | +54.2% | 0.997 |
| tv_linear | 73.0 | 114.3 | +56.6% | 0.961 |
| direct_mail | 49.1 | 36.9 | -24.7% | 0.894 |
| affiliate | 70.7 | 64.0 | -9.5% | 0.996 |
| influencer | 69.3 | 79.7 | +15.0% | 0.987 |
| **Overall** | **586.5** | **561.3** | **-4.3%** | |

## Tech Stack

- [PyMC-Marketing](https://github.com/pymc-labs/pymc-marketing) 0.17.1
- PyMC 5.24.1 / PyTensor 2.31.7 / ArviZ 0.21.0
- Python 3.11

All models: 4 chains, 2k tune + 2k draws, NUTS, `target_accept=0.95`, `max_treedepth=15`. Zero divergences across all 4 models.

## Repository Structure

```
├── Hierarchical_MMM/
│   ├── mmm_example_custom_hierarchical.ipynb   # Full experiment notebook
│   ├── blog_cascading_hierarchical_mmm.md      # Write-up with analysis
│   ├── setup.pdf                               # Setup reference
│   └── output_plots/                           # All generated figures
```
