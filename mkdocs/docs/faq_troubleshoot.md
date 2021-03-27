# Frequently asked questions and troubleshooting

## Frequently asked questions (FAQs)

#### Does Human-GEM contain an ATP maintenance reaction?
Yes, `HMR_3964` is an ATP hydrolysis reaction:
```matlab
constructEquations(ihuman, 'HMR_3964')

ans =

  1×1 cell array

    {'ATP[c] + H2O[c] => ADP[c] + H+[c] + Pi[c]'}
```


## Troubleshooting

#### Biomass flux is zero
**Problem:** Running FBA on Human-GEM or a model derived from Human-GEM with a biomass reaction as the objective results in a solution with zero flux, even when all exchange reaction bounds are open.

**Solution:** Convert the model to "open" format, meaning that it does not contain any "boundary" ("unconstrained") metabolites.
```matlab
% using RAVEN: use the simplifyModel function
model = simplifyModel(model);

% using COBRA: find and remove all boundary metabolites
[~, boundary_comp_index] = ismember('Boundary', model.compNames);
if isfield(model, 'metComps')
    model = removeMetabolites(model, model.mets(model.metComps == boundary_comp_index));
end
```


## GitHub Discussions

For additional questions and discussions related to Human-GEM, check out the [Discussions page](https://github.com/SysBioChalmers/Human-GEM/discussions) within the Human-GEM GitHub repository! Feel free to submit a question there if you cannot find a solution to your problem.


