# Installation

## Human-GEM

The Human-GEM model is maintained in a [GitHub repository](https://github.com/SysBioChalmers/Human-GEM) to facilitate tracking of changes and collaboration. In addition to several different model formats (e.g., `.mat`, `.xml`, `.yml`), the repository contains a number of functions and data files to factilitate Human-GEM analysis, maintenance, editing, and other actions. We therefore recommend cloning the GitHub repository to gain full functionality and easy access to the most updated version of Human-GEM.

#### Clone the repository (recommended)
Clone the repository using git. Navigate to the directory where you would like to save the Human-GEM repository, and in the command line/terminal run:

```bash
git clone https://github.com/SysBioChalmers/Human-GEM.git
```

#### Alternative: Download as a .zip
The latest release of the repository can be downloaded as a [.zip file](https://github.com/SysBioChalmers/Human-GEM/releases/latest).


## Required software

### MATLAB (R2018 or newer)
([30-day trial](https://www.mathworks.com/campaigns/products/trials.highResolutionDisplay.html))

### The RAVEN Toolbox (v2.3.1 or newer)
[Installation instructions](https://github.com/SysBioChalmers/RAVEN/wiki/Installation#installation)


## Additional dependencies

### Solver

To run flux balance analysis (FBA) or GEM extraction (using tINIT), a solver is required. A few options are listed below.

#### Gurobi (recommended)

#### Mosek

#### GLPK


### The COBRA Toolbox

The COBRA Toolbox is not required for most of the functions associated with Human-GEM. However, it offers many analysis tools that can be used with Human-GEM.

!!! important
	The Human-GEM model files (`.mat`, `.xml`, `.yml`) are formatted for use with RAVEN. Before using the model with the COBRA toolbox, convert the model to a COBRA-friendly format in MATLAB using the `ravenCobraWrapper` function from the RAVEN Toolbox:  
	`cobraModel = ravenCobraWrapper(ravenModel);`





