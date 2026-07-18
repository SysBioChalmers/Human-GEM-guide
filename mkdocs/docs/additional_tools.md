# Additional tools and features

The Human-GEM model and repository contain many tools and features that are not covered in detail (or at all) in the examples presented in this guide. These features are described below.


### Reaction, metabolite, and gene annotations

In addition to the model files, the `model/` directory of the Human-GEM repository provides annotation files that link Human-GEM identifiers to a wide range of external databases:

- **`reactions.tsv`** — reaction cross-references, including KEGG, BiGG, MetaNetX, Reactome, Recon3D, HepatoNET1, EHMN, HMR2, TCDB, and **Rhea** identifiers, plus spontaneous-reaction flags.
- **`metabolites.tsv`** — metabolite cross-references, including BiGG, KEGG, HMDB, ChEBI, PubChem, LipidMaps, MetaNetX, Recon3D, and Metabolic Atlas (**MA**) identifiers.
- **`genes.tsv`** — Ensembl gene identifiers.

In Human2 these cross-references were substantially expanded, and the metabolite structures (formulas and charges) were verified and standardized at physiological pH 7.3. Each `.tsv` file shares the same row order as the corresponding `model.rxns`, `model.mets`, or `model.genes` field, so the annotations can be joined to the model directly.


### Gene ID/name conversion

The `translateGrRules` function enables quick and easy conversion of model gene IDs to a different type, such as gene abbreviations, NCBI (Entrez) IDs, or UniProt IDs.
```matlab
% generate model gene-related fields that use gene names (abbreviations)
[grRules, genes, rxnGeneMat] = translateGrRules(model.grRules, 'Name');

% update the model fields with new gene ID type
model.grRules = grRules;
model.genes = genes;
model.rxnGeneMat = rxnGeneMat;
```

In some cases, ID associations are non-unique or missing; e.g., one Ensembl ID is associated with multiple gene names, one gene name is associated with multiple Ensembl IDs, or there is no gene name corresponding to an Ensembl ID. Such cases are handled as follows:

**1. Original ID is associated with multiple new IDs**

The original ID will be replaced with all matching new IDs, which are then formulated as isozymes. For example, if the original grRule is
```
'geneA and geneB'
```
and `geneB` is associated with `id1`, `id2`, and `id3`, the new grRule will be
```
'geneA and (id1 or id2 or id3)'
```

**2. Multiple original IDs are associated with the same new ID**

The original IDs will be replaced with this new ID, and any duplications in grRules will be automatically detected and removed. For example, if the original grRule is
```
'geneA or geneB or geneC'
```
and `geneB` and `geneC` are both associated with `id1`, the new grRule will be
```
'geneA or id1'
```

**3. Original ID is not associated with any new IDs**

In this case, the original ID will be removed from the model. Such cases will therefore result in information loss if genes are converted to new IDs and then again back to the original IDs.

!!! note
	The `translateGrRules` function has an optional `noMatch` input, where users can change how IDs without a match are treated. This input defaults to `'delete'`, but if `'original'` is specified, it will simply keep the original ID. Be aware that this will result in mixed ID types, which may be problematic.











