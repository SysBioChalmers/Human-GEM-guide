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

**Solution:** Ensure that the model is in "simulation" format, meaning that all boundary metabolites (those in the `x` compartment) have been removed.
```matlab
% get coundary compartment index
[~, boundary_comp_index] = ismember('x', model.comps);

% check if any metabolites are in the boundary compartment - should be FALSE (0)
any(model.metComps == boundary_comp_index)

% ans =
% 
%   logical
% 
%    0
```



