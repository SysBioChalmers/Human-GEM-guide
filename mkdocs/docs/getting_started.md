# Getting Started

Make sure you have completed the [installation process](installation.md) before continuing. The commands described on this page are all run from the MATLAB command prompt, unless otherwise noted.

## Loading Human-GEM into MATLAB

!!! important
	The Human-GEM model files are provided in a **RAVEN-friendly format**. If you intend on using **COBRA**, see the [Using Human-GEM with COBRA](#using-human-gem-with-cobra) section below.


!!! note
	All the model file formats described below are on the `master` branch of the Human-GEM repository. Note that only the `.yml` version is available on branches other than `master` (e.g., `devel`), to facilitate tracking of model changes.

#### From the `HumanGEM.mat` file (recommended)

The quickest and easiest way to load the Human-GEM model is from the `.mat` file.
```matlab
load('HumanGEM.mat');
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
%                    rxns: {13417×1 cell}
%                    mets: {10138×1 cell}
%                       S: [10138×13417 double]
%                      lb: [13417×1 double]
%                      ub: [13417×1 double]
%                     rev: [13417×1 double]
%                       c: [13417×1 double]
%                       b: [10138×1 double]
%                   comps: {10×1 cell}
%               compNames: {10×1 cell}
%                rxnNames: {13417×1 cell}
%                 grRules: {13417×1 cell}
%              rxnGeneMat: [13417×3625 double]
%              subSystems: {13417×1 cell}
%                 eccodes: {13417×1 cell}
%                   genes: {3625×1 cell}
%                metNames: {10138×1 cell}
%                metComps: [10138×1 double]
%                  inchis: {10138×1 cell}
%             metFormulas: {10138×1 cell}
%           unconstrained: [10138×1 double]
%           rxnReferences: {13417×1 cell}
%                 rxnFrom: {13417×1 cell}
%                 metFrom: {10138×1 cell}
%     rxnConfidenceScores: [13417×1 double]
%              metCharges: [10138×1 int64]
%                 version: '1.3.1'
%              annotation: [1×1 struct]
```


#### From the `HumanGEM.yml` file (recommended if not on `master` branch)

The yaml version of Human-GEM is loaded using the `importHumanYaml` function.
```matlab
ihuman = importHumanYaml('HumanGEM.yml');

% Start importing...
%         metaData
%         metabolites
%         reactions
%         genes
%         compartments
% 
% importing completed
% follow-up processing... Done!
```


#### From the `HumanGEM.xml` (SBML) file

The `.xml` (SBML) version of Human-GEM is loaded using the RAVEN `importHumanYaml` function.
```matlab
ihuman = importModel(which('HumanGEM.xml'));

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



#### From the `HumanGEM.xlsx` file

The `.xslx` (Excel) version of Human-GEM is loaded using the RAVEN `importExcelModel` function.
```matlab
ihuman = importExcelModel(which('HumanGEM.xlsx'));

% WARNING: Potentially problematic ") AND (" in the grRules for reaction(s): 
%         HMR_7161
%         HMR_7162
%         ATPasel
% 
% WARNING: The following InChI strings are associated to more than one unique metabolite name:
%         1S/C27H46O2/c1-18(2)7-6-14-27(5,29)24-11-10-22-21-9-8-19-17-20(28)12-15-25(19,3)23(21)13-16-26(22,24)4/h8,18,20-24,28-29H,6-7,9-17H2,1-5H3/t20-,21-,22-,23-,24-,25-,26-,27-/m0/s1
%         1S/C42H58NO/c1-32(20-22-39-35(4)17-12-25-41(39,6)7)14-10-16-34(3)30-38-31-37(24-27-43(38)28-29-44)19-11-15-33(2)21-23-40-36(5)18-13-26-42(40,8)9/h10-11,14-16,19-24,27,30-31,44H,12-13,17-18,25-26,28-29H2,1-9H3/q+1

```

!!! important
	By default, `importExcelModel` will remove the boundary metabolites (those in the `x` compartment) from the model. To prevent removal of the boundary metabolites, specify `false` in the second argument: `importExcelModel('HumanGEM.xlsx', false)`.


#### From the `HumanGEM.txt` file

There is no function to import the `.txt` version of the model. The `HumanGEM.txt` file is supplied for those who need or prefer a more human-readable plain-text format, but is not intended for loading into MATLAB.



## Using Human-GEM with COBRA

Using the provided Human-GEM models directly with COBRA is likely to cause errors or unexpected behavior. Therefore, the model should first be converted to a COBRA-friendly format before using the COBRA Toolbox.

#### 1. Load the model into MATLAB
Use one of the methods described [above](#loading-human-gem-into-matlab) to load Human-GEM into MATLAB.
```matlab
load('HumanGEM.mat');  % loads model as structure named "ihuman"
```

#### 2. Convert the model into COBRA format
Use the RAVEN `ravenCobraWrapper` function to convert the model into a COBRA-friendly format.
```matlab
model = ravenCobraWrapper(ihuman);

%  Converting RAVEN structure to COBRA..
```

The resulting `model` output should now be ready for use with the COBRA Toolbox.

#### Alternative approach (not recommended)
Another option is to load the SBML model `HumanGEM.xml` directly into COBRA format using the COBRA `readCbModel` function.
```matlab
model = readCbModel(which('HumanGEM.xml'));

% The model contains 0 errors and 1 warnings.
```
!!! warning
	Since the SBML file is not formatted for COBRA, the resulting model may be missing content or exhibit unexpected behavior. Proceed with caution.











