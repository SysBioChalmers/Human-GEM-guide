# Installation

## Human-GEM

The Human-GEM model is maintained in a [GitHub repository](https://github.com/SysBioChalmers/Human-GEM) to track changes and facilitate collaboration. In addition to several different model formats (e.g., `.mat`, `.xml`, `.yml`), the repository contains a number of functions and data files that support Human-GEM analysis, maintenance, editing, and other actions. We therefore recommend cloning the GitHub repository to gain full functionality and easy access to the most updated version of Human-GEM.

!!! note
    Although the functions contained within the Human-GEM repository require **MATLAB**, the model itself can still be used with other languages such as **Python** (see [section below on COBRApy](#cobrapy)).

#### 1. Clone the repository (recommended)
Clone the repository using [git](https://git-scm.com/). Navigate to the directory where you would like to save the Human-GEM repository, and in the command line/terminal run:

```bash
git clone https://github.com/SysBioChalmers/Human-GEM.git
```

This will create a copy of the repository contents on your local machine.

#### 1b. Alternative: Download as a .zip
The latest release of the repository can be downloaded as a [.zip or .tar.gz file](https://github.com/SysBioChalmers/Human-GEM/releases/latest). Download the file using the link, and unzip/expand the file.

#### 2. Add the repository to the MATLAB path
In order for MATLAB to find and use the functions and files in the repository directory (and subdirectories), they need to be added to the PATH. In MATLAB, select `HOME` from the top menu bar, and choose `Set Path` and then `Add with Subfolders...`. Choose the `code/`, `data/`, and `model/` subdirectories in the `Human-GEM` folder to be added to the path, and click `Save` when finished.

Alternatively, you can run the following lines in MATLAB:
```matlab
cd /my/path/Human-GEM/code  % replace "/my/path/" with the actual path to the Human-GEM folder
HumanGEMInstaller.install
savepath
```
To remove `Human-GEM` from the PATH, the following command can be used: `HumanGEMInstaller.uninstall`

Ignore the `Warning: Name is nonexistent or not a directory` messages.


## Required software

#### MATLAB
**[R2018 or newer]**  
MATLAB is the primary software used for working with Human-GEM. Unfortunately, MATLAB is not free, though a ([30-day trial](https://www.mathworks.com/campaigns/products/trials.highResolutionDisplay.html)) is available.

#### The RAVEN Toolbox
**[v2.7.5 or newer]**  
The Human-GEM model files are provided in RAVEN-friendly format, and many of the functions in the Human-GEM repository depend on the RAVEN Toolbox. Ensure that the RAVEN Toolbox is installed and added to your MATLAB path. See the [RAVEN installation instructions](https://github.com/SysBioChalmers/RAVEN/wiki/Installation#installation) for more detail.

#### The COBRA Toolbox (optional)
The [COBRA Toolbox](https://opencobra.github.io/cobratoolbox/stable/) is not required for most of the functions associated with Human-GEM. However, it offers many additional analysis tools that are not available in RAVEN, and can for the most part be used instead of RAVEN.

!!! important
	The Human-GEM model files were originally designed for use with the RAVEN Toolbox. Although the model should be compatible with most COBRA functions as-is, it may be necessary to first convert the model to a COBRA-friendly format in MATLAB using the `ravenCobraWrapper` function from the RAVEN Toolbox:
	`cobraModel = ravenCobraWrapper(ravenModel);`

!!! note
	There is an [ongoing effort](https://github.com/SysBioChalmers/RAVEN/issues/184) to align the model structures of RAVEN and COBRA to eliminate compatibility issues, so hopefully this will soon be a problem of the past.


#### Linear optimization solver
A solver is required to run flux balance analysis (FBA), GEM extraction (using tINIT), or any other function involving an optimization. Compatible solvers are Gurobi Optimizer (recommended), GLPK, or the COBRA solver.

!!! important
    We **strongly recommend** using the Gurobi Optimizer, as issues are sometimes observed for other solvers since Human-GEM is a relatively large model.

For instructions on how to set up a solver and obtain a license (if required), see the [RAVEN instructions](https://github.com/SysBioChalmers/RAVEN/wiki/Installation#dependencies).


## Python

In addition to MATLAB, the Human-GEM model and the workflows in this guide can be used from Python. Three packages are used across this guide:

**[COBRApy](https://opencobra.github.io/cobrapy/)** performs constraint-based reconstruction and analysis (loading the model, FBA, FVA, etc.). It is stable and available on PyPI:
```bash
pip install cobra
```

!!! note
    Load Human-GEM from the SBML (`Human-GEM.xml`) file when using COBRApy; it does not read the RAVEN YAML format directly. See the [COBRApy Documentation](https://cobrapy.readthedocs.io/en/stable/) for more detail.

**[raven-toolbox](https://github.com/SysBioChalmers/raven-toolbox)** is a Python implementation of the RAVEN Toolbox, providing reconstruction and analysis functions such as ftINIT model extraction and model comparison.

**[geckopy](https://github.com/SysBioChalmers/geckopy)** is a Python implementation of GECKO for enzyme-constrained models (see [Enzyme-constrained models](enzyme_constrained.md)).

!!! warning "Experimental"
    `raven-toolbox` and `geckopy` are under active development and are not yet released on PyPI. Install them from GitHub `main`, and be aware that their APIs may still change:
    ```bash
    pip install "git+https://github.com/SysBioChalmers/raven-toolbox.git@main"
    pip install "git+https://github.com/SysBioChalmers/geckopy.git@main"
    ```




