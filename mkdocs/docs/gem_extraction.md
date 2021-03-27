# GEM Extraction

Human-GEM is a generic model of human metabolism, meaning that it contains metabolic reactions known to occur in any human cell. The model is therefore not representative of any one tissue or cell type, in which only a subset of the reactions would be active.

A common approach is to extract the subset of the GEM that is likely to be active in the tissue or cell type of interest, based on a corresponding dataset (e.g., transcriptomics, proteomics, metabolomics, etc.). This approach is often referred to as *GEM extraction* or *contextualization*, where the generated model is termed the *extracted* or *contextualized*/*context-specific GEM*.


## Task‐driven Integrative Network Inference for Tissues (tINIT)

Although many GEM extraction methods exist, this guide will cover the tINIT algorithm. For more details on the algorithm itself, see the following papers:

1. [Agren *et al.* (2012) *PLoS Comput Biol*](https://doi.org/10.1371/journal.pcbi.1002518) - Initial development of INIT
2. [Agren *et al.* (2014) *Mol Syst Biol*](https://doi.org/10.1002/msb.145122) - Incorporation of metabolic tasks (tINIT)
3. [Robinson *et al.* (2020) *Sci Signal*](https://doi.org/10.1126/scisignal.aaz1482) - Recognition of enzyme complexes

The original (2014) tINIT algorithm is implemented in the RAVEN Toolbox, where its main calling function is `getINITModel`. This guide will cover the updated (2020) version of tINIT that is maintained on the Human-GEM repository, called using the `getINITModel2` function.


## Retrieve the data

To demonstrate the use of tINIT, we will walk through an example that replicates part of the [Human-GEM publication](https://doi.org/10.1126/scisignal.aaz1482). 

The data used in this example can be retrieved from the [Zenodo repository](https://doi.org/10.5281/zenodo.3577466) associated with the publication. Download the Zenodo repository .zip file, and extract.


## Prepare the transcriptomic data

Data from the condition, tissue, or cell type for which the GEM will be generated is necessary to determine which reactions from the reference model (Human-GEM) should be included. In this case, we are using RNA-Seq data (TPM normalized) that was originally obtained from the [GTEx database](https://gtexportal.org/home/). 

In MATLAB, load the `gtex_median_tissue_tpm.txt` file from the Zenodo repository `tINIT_GEMs/data/` subdirectory.

```matlab
% replace 'my/path/' with the path on your system, or change to the directory with the file
gtex_data = readtable('my/path/tINIT_GEMs/data/gtex_median_tissue_tpm.txt');

% take a look at the first few rows and columns of the table
gtex_data(1:5,1:5)

% ans =
% 
%   5×5 table
% 
%            genes           adipose_tissue    adrenal_gland    blood_vessel    bladder
%     ___________________    ______________    _____________    ____________    _______
% 
%     {'ENSG00000223972'}       0.05458            0.0746         0.04535       0.05878
%     {'ENSG00000227232'}          11.2             8.023           11.99         14.24
%     {'ENSG00000243485'}       0.06012           0.08179         0.05278       0.06097
%     {'ENSG00000237613'}       0.03653            0.0405         0.03668       0.04113
%     {'ENSG00000268020'}       0.02565           0.03479               0             0
```

Extract information from the table into a structure called `data_struct`
```matlab
% extract the tissue and gene names
data_struct.tissues = gtex_data.Properties.VariableNames(2:end)';  % sample (tissue) names
data_struct.genes = gtex_data.genes;  % gene names
data_struct.levels = table2array(gtex_data(:, 2:end));  % gene TPM values
```

!!! important
	The name of the data structure (`data_struct` in this example) is not important. However, its fields (`genes`, `tissues`, `levels`) should **not** be changed.

!!! note
	We don't actually need all of the data for this example since we are only generating a model for one tissue (liver). One could instead extract only the `liver` column into the `levels` field, and included only `liver` in the `tissues` field.

Although the original tINIT implementation always compared gene expression in the tissue of interest to the average of all other provided tissues, the updated version enables the option to instead compare the expression to a threshold value. Here, we will use this alternative approach with a threshold value of 1 TPM.
```matlab
data_struct.threshold = 1;
```

!!! note
	Exclude the `threshold` field from the data structure to use the original tINIT approach of comparing the gene expression in your tissue of interest with all other tissues. If you choose this approach, however, you _**must include the gene expression data from the other tissues**_ so that the algorithm has something to compare to.

Take a look at `data_struct` to make sure all the fields are present and have the expected dimensions
```matlab
data_struct

% data_struct =
%
%   struct with fields:
% 
%       tissues: {30×1 cell}
%         genes: {56202×1 cell}
%        levels: [56202×30 double]
%     threshold: 1
```

## Load and prepare the reference GEM

The reference GEM from which the liver-specific model will be extracted is Human-GEM. Load the model from the `Human-GEM.mat` file in the Human-GEM repository
```matlab
load('Human-GEM.mat');  % loads model as a structure named "ihuman"
```

!!! important
	The metabolic task evaluation and tINIT algorithms requires that the model is provided in "closed form"; i.e., it contains **boundary metabolites**. Therefore, the boundary metabolites must be added to the GEM before proceeding.

Add boundary metabolites to the model
```matlab
ihuman = addBoundaryMets(ihuman);

% Boundary metabolites were added to 1665 reactions.
% New (boundary) versions of 1665 metabolites were added to the model.
```

!!! note
    The exchange reactions should all be unbounded (e.g., lower and upper bounds of -1000 and 1000, respectively). Human-GEM is already provided in this format, so no additional changes are needed.


## Prepare the metabolic task list

The tINIT algorithm has the option to specify one or more metabolic tasks that the extracted model should be able to perform, such as transport of essential amino acids or production of biomass. Although task specification is not required, it is generally recommended to obtain a more functional extracted GEM, especially if it is going to be used in simulation-based analysis (e.g., FBA).

Following with the procedure used in the Human-GEM manuscript, this example will use the list of 57 "essential" metabolic tasks that all cells are expected to be able to perform to be viable.

Load the `metabolicTasks_Essential.xlsx` list of metabolic tasks from the `data/metabolicTasks/` subdirectory of the Human-GEM repository using the RAVEN `parseTaskList` function.
```matlab
essentialTasks = parseTaskList('/my/path/Human-GEM/data/metabolicTasks/metabolicTasks_Essential.xlsx')

% essentialTasks = 
% 
%   57×1 struct array with fields:
% 
%     id
%     description
%     shouldFail
%     printFluxes
%     comments
%     inputs
%     LBin
%     UBin
%     outputs
%     LBout
%     UBout
%     equations
%     LBequ
%     UBequ
%     changed
%     LBrxn
%     UBrxn
```

It is important to first verify that the reference model (Human-GEM) can successfully perform all of the tasks. If the reference model cannot perform a task, then neither can any GEM extracted from that model.

Verify that the reference model passes all of the essential metabolic tasks.
```matlab
% see what the other inputs mean by typing "help checkTasks"
checkTasks(ihuman, [], true, false, false, essentialTasks);

% PASS: [ER] Aerobic rephosphorylation of ATP from glucose
% PASS: [ER] Aerobic rephosphorylation of GTP
% PASS: [ER] Aerobic rephosphorylation of CTP
% PASS: [ER] Aerobic rephosphorylation of UTP
% PASS: [BS] ATP de novo synthesis
% PASS: [BS] CTP de novo synthesis
% PASS: [BS] GTP de novo synthesis
% PASS: [BS] UTP de novo synthesis
% PASS: [BS] dATP de novo synthesis
% ...
```


## Run tINIT

Now all inputs are ready to run tINIT and extract a GEM specific to the liver tissue based on its corresponding RNA expression profile. 

Although it's not necessary to re-assign the input variables before calling the `getINITModel2` function, we do it here to make the inputs very clear. Use the `help getINITModel2` command to see more details on the function inputs and outputs.

```matlab
refModel = ihuman;  % the reference model from which the GEM will be extracted
tissue = 'liver';  % must match the tissue name in data_struct.tissues
celltype = [];  % used if tissues are subdivided into cell type, which is not the case here
hpaData = [];  % data structure containing protein abundance information (not used here)
arrayData = data_struct;  % data structure with gene (RNA) abundance information
metabolomicsData = [];  % list of metabolite names if metabolomic data is available
removeGenes = true;  % (default) remove lowly/non-expressed genes from the extracted GEM
taskFile = [];  % we already loaded the task file, so this input is not required
useScoresForTasks = true;  % (default) use expression data to decide which reactions to keep
printReport = true;  % (default) print status/completion report to screen
taskStructure = essentialTasks;  % metabolic task structure (used instead "taskFile")
params = [];  % additional optimization parameters for the INIT algorithm
paramsFT = [];  % additional optimization parameters for the fit-tasks algorithm
```

Run the getINITModel2 function to extract the GEM
```matlab
liverGEM = getINITModel2(refModel, tissue, celltype, hpaData, arrayData, metabolomicsData, removeGenes, taskFile, useScoresForTasks, printReport, taskStructure, params, paramsFT);

% ***Generating model for: liver
% -Using array data
% -Using metabolic tasks
% 
% Reference model statistics:
%         13417 reactions, 3625 genes
%         Mean reaction score: 4.1017
%         Mean gene score: 5.3912
%         Reactions with positive scores: 54.2372%
%
%  Pruned model statistics:
%         11903 reactions, 3052 genes
%         Mean reaction score: 4.1094
%         Mean gene score: 5.1854
%         Reactions with positive scores: 54.1964%
%  
%  Reactions essential for tasks:
%         403 reactions, 365 genes
%         Mean reaction score: 8.3307
%         Mean gene score: 7.1855
%         Reactions with positive scores: 87.8412%

MILP detected.
Academic license - for non-commercial use only
Gurobi Optimizer version 9.0.1 build v9.0.1rc0 (mac64)
Optimize a model with 25830 rows, 60561 columns and 126189 nonzeros
...
```

!!! warning
	This algorithm will take a while to run (30 min to 1 hr). It is therefore recommended that this is run on a compute cluster, especially when repeating for many different GEMs.

!!! note
	In this example, we called `getINITModel2` with one output, `liverGEM`. Use `help getINITModel2` to see a description of the additional optional outputs.


Once the optimization is complete, we can take a look at the extracted GEM.
```matlab
liverGEM

% liverGEM = 
% 
%   struct with fields:
% 
%                      id: 'INITModel'
%             description: 'Automatically generated model for liver'
%                    rxns: {7971×1 cell}
%                    mets: {6744×1 cell}
%                       S: [6744×7971 double]
%                      lb: [7971×1 double]
%                      ub: [7971×1 double]
%                     rev: [7971×1 double]
%                       c: [7971×1 double]
%                       b: [6744×2 double]
%                   comps: {10×1 cell}
%               compNames: {10×1 cell}
%                rxnNames: {7971×1 cell}
%                 grRules: {7971×1 cell}
%              rxnGeneMat: [7971×2305 double]
%              subSystems: {7971×1 cell}
%                 eccodes: {7971×1 cell}
%                   genes: {2305×1 cell}
%                metNames: {6744×1 cell}
%                metComps: [6744×1 double]
%                  inchis: {6744×1 cell}
%             metFormulas: {6744×1 cell}
%           unconstrained: [6744×1 double]
%           rxnReferences: {7971×1 cell}
%                 rxnFrom: {7971×1 cell}
%                 metFrom: {6744×1 cell}
%     rxnConfidenceScores: [7971×1 double]
%              metCharges: [6744×1 int64]
%                 version: '1.3.1'
%              annotation: [1×1 struct]
%                geneFrom: {2305×1 cell}
```

It is recommended to change the model `id` to a more descriptive name than the default of "INITModel". This is particularly useful when analyzing several models together.
```matlab
liverGEM.id = 'liver';
```

Notice that the model has fewer reactions, metabolites, and genes compared to the reference model.
```matlab
numel(ihuman.rxns) - numel(liverGEM.rxns)

% ans =
% 
%         5446

numel(ihuman.mets) - numel(liverGEM.mets)

% ans =
% 
%         3394

numel(ihuman.genes) - numel(liverGEM.genes)

% ans =
% 
%         1320
```

It is also good to verify that the extracted GEM can also perform the required metabolic tasks.
```matlab
checkTasks(liverGEM, [], true, false, false, essentialTasks);
```




