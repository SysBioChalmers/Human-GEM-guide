# Additional tools and features

The Human-GEM model and repository contain many tools and features that are not covered in detail (or at all) in the examples presented in this guide. These features are described below.


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









