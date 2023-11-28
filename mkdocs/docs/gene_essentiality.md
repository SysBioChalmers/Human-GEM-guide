# Gene essentiality with DepMap

!!! warning
    The writing of these instructions is a work in progress, originality created through the pull request https://github.com/SysBioChalmers/Human-GEM/pull/574/. The instructions are not guaranteed to work, and assume background knowledge.

## Setup

First make sure you have the DepMap.org data - we currently use DepMap version 2021Q3. You need to go to the downloads section at [DepMap](https://depmap.org/portal/download/all/), select `21Q3`.

Move into `Human-GEM/code/DepMapGeneEss/data` the following downloaded files:

- `Achilles_gene_effect.csv`
- `CCLE_expression_full.csv`

## Data wrangling

1. Open the script `ConvertGeneEssInputData.R` in RStudio and run it. This will generate the file `DepMap_tpm_gene_symbols.txt`.
2. Run `PrepDepMapData.m`. This will take an hour or so and generates the files:
    - `arrayDataDepMap.mat`
    - `prepDataGeneSymbols.mat`


## Cluster computation

### Configure dependencies

1. Create a folder on the cluster with a suitable name, for example `GeneEssDepMap`. Within that folder, create a folder named `components`.
2. Copy RAVEN, COBRA, and Human-GEM to `components` - they need to reside under those exact names, because those and underlying folders will be added to the Matlab path.
3. Make sure the Human-GEM folder `Human-GEM/code/DepMapGeneEss/data` is copied as well with the files generated above under _Setup_.
4. Create a folder `DepMapRuns` in the top folder `GeneEssDepMap`. Copy in this new folder the files:
    - `run_gen_depmap_models.sh`
    - `run_evaless_depmap.sh`
    - `generate_DepMap_models_ftINIT_Cluster.m`
    - `getTaskEssentialGenesCluster.m`

Also create a folder called `logs` in the same folder.

### Run cluster jobs

1. Make sure you are in the `DepMapRuns` folder. First, generate the models using the following line (also available in the .sh script):

```bash
sbatch -o logs/run_gen_depmap_models-%A-%a.log --array=1-40 run_gen_depmap_models.sh
```

It may be needed to inititialize the RAVEN, COBRA and GECKO toolboxes first. Each of these has their own installation command. Also remember to choose the solver for the RAVEN toolbox.

2. After the model generation has completed, run:

```bash
sbatch -o logs/run_evaless_depmap-%A-%a.log --array=1-40 run_evaless_depmap.sh
```

3. Download all produced files to the data folder on your computer. These are found in `components/Human-GEM/code/DepMapGeneEss/data`.

4. Run `evaluateDepMapEssentialityPredictions.m`. Note that you may need to change the folder in the file and that there are some things in the file that need to be modified to match what you want to do. The file currently only gets stats for one model version, but can easily be expanded to take in data from more model versions, although these also need to be processed as above as well.

5. Run `PlotGeneEss.R` in RStudio to make a violin plot of MCC for each model. The code needs some modification to fix paths and number of models.
