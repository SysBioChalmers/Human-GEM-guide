# Getting Started

Make sure you have completed the [installation process](installation.md) before continuing. The commands described on this page are all run from the MATLAB command prompt, unless otherwise noted.

## Loading Human-GEM into MATLAB

!!! important
	The Human-GEM model files are provided in a **RAVEN-friendly format**. If you intend on using **COBRA**, see the [Using Human-GEM with COBRA](#using-human-gem-with-cobra) section below.


!!! note
	All the model file formats described below are on the `master` branch of the Human-GEM repository. Note that only the `.yml` version is available on branches other than `master` (e.g., `devel`), to facilitate tracking of model changes.

#### From the `Human-GEM.mat` file (recommended)

The quickest and easiest way to load the Human-GEM model is from the `.mat` file.
```matlab
load('Human-GEM.mat');
```

This will load the model as a structure named `ihuman`.
```matlab
ihuman

% ihuman = 
% 
%   struct with fields:
% 
%                      id: 'Human-GEM'
%             description: 'Generic genome-scale metabolic model of Homo sapiens'
%                    rxns: {13416×1 cell}
%                    mets: {8458×1 cell}
%                       S: [8458×13416 double]
%                      lb: [13416×1 double]
%                      ub: [13416×1 double]
%                     rev: [13416×1 double]
%                       c: [13416×1 double]
%                       b: [8458×1 double]
%                   comps: {9×1 cell}
%               compNames: {9×1 cell}
%                rxnNames: {13416×1 cell}
%                 grRules: {13416×1 cell}
%              rxnGeneMat: [13416×3628 double]
%              subSystems: {13416×1 cell}
%                 eccodes: {13416×1 cell}
%                   genes: {3628×1 cell}
%                metNames: {8458×1 cell}
%                metComps: [8458×1 double]
%                  inchis: {8458×1 cell}
%             metFormulas: {8458×1 cell}
%           rxnReferences: {13416×1 cell}
%                 rxnFrom: {13416×1 cell}
%                 metFrom: {8458×1 cell}
%     rxnConfidenceScores: [13416×1 double]
%              metCharges: [8458×1 int64]
%                 version: '1.4.0'
%              annotation: [1×1 struct]
```


#### From the `Human-GEM.yml` file (recommended if not on `master` branch)

The yaml version of Human-GEM is loaded using the `importHumanYaml` function.
```matlab
ihuman = importHumanYaml('Human-GEM.yml');

% Start importing...
%   1
%   2
%   3
%   4
%   5
% 
% importing completed
% follow-up processing... Done!
```


#### From the `Human-GEM.xml` (SBML) file

The `.xml` (SBML) version of Human-GEM is loaded using the RAVEN `importHumanYaml` function.
```matlab
ihuman = importModel(which('Human-GEM.xml'));

% The model contains 0 errors and 1 warnings.
% 
% WARNING: Potentially problematic ") AND (" in the grRules for reaction(s): 
%         HMR_7161
%         HMR_7162
%         ATPasel
```

!!! note
	The warning regarding the `grRules` can be ignored. Some packages are a bit more strict in their gene-reaction rule formulation, but Human-GEM functions have no such restrictions.

!!! note
	We include the `which()` command around the model filename to provide its full path because some functions cannot otherwise find the file. This is not necessary if the model file is in MATLAB's current working directory (i.e., if you can see the model file in the `Current Folder` window).



#### From the `Human-GEM.xlsx` file

The `.xslx` (Excel) version of Human-GEM is loaded using the RAVEN `importExcelModel` function.
```matlab
ihuman = importExcelModel(which('Human-GEM.xlsx'));

% WARNING: Potentially problematic ") AND (" in the grRules for reaction(s): 
%         HMR_7161
%         HMR_7162
%         ATPasel
% 
% WARNING: The following InChI strings are associated to more than one unique metabolite name:
%         1S/C27H46O2/c1-18(2)7-6-14-27(5,29)24-11-10-22-21-9-8-19-17-20(28)12-15-25(19,3)23(21)13-16-26(22,24)4/h8,18,20-24,28-29H,6-7,9-17H2,1-5H3/t20-,21-,22-,23-,24-,25-,26-,27-/m0/s1
%         1S/C42H58NO/c1-32(20-22-39-35(4)17-12-25-41(39,6)7)14-10-16-34(3)30-38-31-37(24-27-43(38)28-29-44)19-11-15-33(2)21-23-40-36(5)18-13-26-42(40,8)9/h10-11,14-16,19-24,27,30-31,44H,12-13,17-18,25-26,28-29H2,1-9H3/q+1

```


#### From the `Human-GEM.txt` file

There is no function to import the `.txt` version of the model. The `Human-GEM.txt` file is supplied for those who need or prefer a more human-readable plain-text format, but is not intended for loading into MATLAB.



## Using Human-GEM with COBRA

The provided Human-GEM models can generally be used directly with COBRA, though there may remain some incompatibilities that cause errors or unexpected behavior. We suggest to first convert the model to a COBRA-friendly format before using the COBRA Toolbox.

#### 1. Load the model into MATLAB
Use one of the methods described [above](#loading-human-gem-into-matlab) to load Human-GEM into MATLAB.
```matlab
load('Human-GEM.mat');  % loads model as structure named "ihuman"
```

#### 2. Convert the model into COBRA format
Use the RAVEN `ravenCobraWrapper` function to convert the model into a COBRA-friendly format.
```matlab
model = ravenCobraWrapper(ihuman);

%  Converting RAVEN structure to COBRA..
```

The resulting `model` output should now be ready for use with the COBRA Toolbox.

#### Alternative approach (not recommended)
Another option is to load the SBML model `Human-GEM.xml` directly into COBRA format using the COBRA `readCbModel` function.
```matlab
model = readCbModel(which('Human-GEM.xml'));

% The model contains 0 errors and 1 warnings.
```
!!! warning
	Since the SBML file is not formatted for COBRA, loading it using `readCbModel` will yield a model that may be missing content or exhibit unexpected behavior. Proceed with caution.











