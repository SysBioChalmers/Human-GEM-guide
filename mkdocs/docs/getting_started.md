# Getting Started

Make sure you have completed the [installation process](installation.md) before continuing.

Examples are given for both **MATLAB** (using the RAVEN Toolbox) and **Python** (using COBRApy); select the corresponding tab in each code block. Unless otherwise noted, the MATLAB commands are run from the MATLAB command prompt.

## Loading Human-GEM

!!! important
	The Human-GEM model files are provided in a **RAVEN-friendly format**. If you intend on using **COBRA** in MATLAB, see the [Using Human-GEM with COBRA](#using-human-gem-with-cobra) section below. In Python, [COBRApy](https://opencobra.github.io/cobrapy/) reads the SBML (`.xml`) file directly.


!!! note
	All the model file formats described below are on the `main` branch of the Human-GEM repository. Note that only the `.yml` version is available on branches other than `main` (e.g., `devel`), to facilitate tracking of model changes.

#### From the `Human-GEM.mat` file (MATLAB) or SBML file (Python)

=== "MATLAB"
	The quickest and easiest way to load the Human-GEM model is from the `.mat` file.
	```matlab
	load('Human-GEM.mat');
	```

	This will load the model as a structure named `humanGEM`.
	```matlab
	humanGEM

	% humanGEM = 
	% 
	%   struct with fields:
	% 
	%                      id: 'HumanGEM'
	%                    name: 'Generic genome-scale metabolic model of Homo sapiens'
	%             description: ''
	%                 version: '2.0.0'
	%                    date: '2026-03-26'
	%              annotation: [1×1 struct]
	%                    rxns: {12931×1 cell}
	%                rxnNames: {12931×1 cell}
	%                    mets: {8461×1 cell}
	%                metNames: {8461×1 cell}
	%                       S: [8461×12931 double]
	%                      lb: [12931×1 double]
	%                      ub: [12931×1 double]
	%                     rev: [12931×1 double]
	%                       c: [12931×1 double]
	%                       b: [8461×1 double]
	%                   genes: {2848×1 cell}
	%                 grRules: {12931×1 cell}
	%              rxnGeneMat: [12931×2848 double]
	%              subSystems: {12931×1 cell}
	%                 eccodes: {12931×1 cell}
	%                rxnNotes: {12931×1 cell}
	%           rxnReferences: {12931×1 cell}
	%     rxnConfidenceScores: [12931×1 double]
	%                metComps: [8461×1 double]
	%                  inchis: {8461×1 cell}
	%             metFormulas: {8461×1 cell}
	%              metCharges: [8461×1 double]
	%                   comps: {9×1 cell}
	%               compNames: {9×1 cell}
	%          geneShortNames: {2848×1 cell}
	%                 metFrom: {8461×1 cell}
	%                 rxnFrom: {12931×1 cell}
	```

=== "Python"
	In Python, load the SBML (`.xml`) version of Human-GEM with COBRApy. (Reading the SBML file takes up to a minute.)
	```python
	import cobra

	model = cobra.io.read_sbml_model('Human-GEM.xml')
	len(model.reactions), len(model.metabolites), len(model.genes)

	# (12931, 8461, 2848)
	```


#### From the `Human-GEM.yml` file

The YAML version is the format tracked across all branches, and is recommended if you are not on the `main` branch.

=== "MATLAB"
	The YAML version of Human-GEM is loaded using the RAVEN `readYAMLmodel` function.
	```matlab
	humanGEM = readYAMLmodel('Human-GEM.yml');
	```

=== "Python"
	COBRApy does not read the RAVEN YAML format directly; use the SBML (`.xml`) file as shown above.


#### From the `Human-GEM.xml` (SBML) file

=== "MATLAB"
	The `.xml` (SBML) version of Human-GEM is loaded using the RAVEN `importModel` function.
	```matlab
	humanGEM = importModel('Human-GEM.xml');

	% Warning: The following fields have prefixes removed from all entries. If this is
	% undesired, run importModel with removePrefix as false. Example:
	%         importModel('filename.xml',[],false);
	```

	!!! note
		The warning regarding removed prefixes can be ignored for normal use. If you need the original identifier prefixes preserved, load the model with `importModel('Human-GEM.xml', [], false)`.

=== "Python"
	```python
	import cobra

	model = cobra.io.read_sbml_model('Human-GEM.xml')
	```


#### From the `Human-GEM.txt` file

There is no function to import the `.txt` version of the model. The `Human-GEM.txt` file is supplied for those who need or prefer a more human-readable plain-text format, but is not intended for loading into MATLAB or Python.



## Using Human-GEM with COBRA

In **Python**, [COBRApy](https://opencobra.github.io/cobrapy/) is itself a COBRA implementation, so the SBML file loaded above is already a COBRA model — no conversion is needed.

In **MATLAB**, the provided Human-GEM models can generally be used directly with the COBRA Toolbox, though there may remain some incompatibilities that cause errors or unexpected behavior. We suggest to first convert the model to a COBRA-friendly format before using the COBRA Toolbox.

#### 1. Load the model into MATLAB
Use one of the methods described [above](#loading-human-gem) to load Human-GEM into MATLAB.
```matlab
load('Human-GEM.mat');  % loads model as structure named "humanGEM"
```

#### 2. Convert the model into COBRA format
Use the RAVEN `ravenCobraWrapper` function to convert the model into a COBRA-friendly format.
```matlab
model = ravenCobraWrapper(humanGEM);

%  Converting RAVEN structure to COBRA..
```

The resulting `model` output should now be ready for use with the COBRA Toolbox.
