# Enzyme-constrained models (ec-Human2)

!!! warning "Work in progress"
    This page describes the enzyme-constrained version of Human-GEM (**ec-Human2**), which is
    being finalized and will be released soon. The instructions below are a draft and are
    likely to change.

Enzyme-constrained GEMs (ecGEMs) augment a metabolic model with enzyme kinetics
(k<sub>cat</sub> values) and a limited enzyme (protein) pool, so that reaction fluxes are
additionally constrained by the abundance and turnover of the enzymes that catalyze them.
This typically shrinks the feasible flux space and improves quantitative predictions. For
Human-GEM, an enzyme-constrained version (**ec-Human2**) is generated with the GECKO method.

As described in [Luo *et al.* (2026) *PNAS*](https://doi.org/10.1073/pnas.2516511123),
ec-Human2 improved flux predictions (e.g., tighter flux variability in NCI-60 cell lines)
and the reproduction of inborn errors of metabolism, and it forms the basis of the
enzyme-constrained whole-body simulations (see [Whole-body models](whole_body_models.md)).

## MATLAB (GECKO)

ec-Human2 is built and analysed with the [GECKO Toolbox](https://github.com/SysBioChalmers/GECKO)
(v3). See the GECKO documentation for the full workflow of constructing an ecModel from a
reference GEM such as Human-GEM.

## Python (geckopy)

!!! warning "Experimental"
    [`geckopy`](https://github.com/SysBioChalmers/geckopy) is a Python implementation of GECKO and
    is under active development; it is not yet on PyPI. Install it from GitHub `main`:
    ```bash
    pip install "git+https://github.com/SysBioChalmers/geckopy@main"
    ```

A full worked example (building ec-Human2, enzyme-constrained FBA/FVA, and inspecting enzyme
usage) will be added here once ec-Human2 is released.
