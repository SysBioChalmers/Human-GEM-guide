# Whole-body models

Human2 serves as the basis for **whole-body models (WBMs)** that connect organ-specific GEMs
through shared biofluid compartments (blood, lumen, bile, cerebrospinal fluid, urine, sweat,
and others) to simulate interorgan metabolite exchange.

As described in [Luo *et al.* (2026) *PNAS*](https://doi.org/10.1073/pnas.2516511123), WBMs were
reconstructed for five sex- and age-specific groups (adult male, adult female, elderly male,
elderly female, and fetus). A simplified **coreWBM**, retaining seven key organs and a unified
blood compartment, and an enzyme-constrained **ec-coreWBM** (see
[Enzyme-constrained models](enzyme_constrained.md)) were used to simulate whole-body energy
metabolism across diets and to model dynamic metabolic adaptation during feeding and fasting
(dynamic FBA).

## Resources

- **Reconstruction code and tutorials:** [LiLabTsinghua/DevelopWBM](https://github.com/LiLabTsinghua/DevelopWBM)
- **Models and data** (tissue/organ models, WBMs, coreWBMs, ec-coreWBMs): [Zenodo record 15117362](https://zenodo.org/records/15117362)

Refer to the [DevelopWBM repository](https://github.com/LiLabTsinghua/DevelopWBM) for
step-by-step instructions on building and simulating whole-body models with Human2.
