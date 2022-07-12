# Flux Balance Analysis

One of the most common analysis methods for GEMs is flux balance analysis (FBA). This page will briefly walk through the basics of using FBA with Human-GEM.

!!! important
	You must have a linear optimization solver (e.g., Gurobi) installed and accessible by MATLAB to run FBA. See the [installation](installation.md) page of this guide or the [RAVEN instructions](https://github.com/SysBioChalmers/RAVEN/wiki/Installation#dependencies) for details on setting up a solver.


## Optimization objective

By default, the model objective (defined by the `.c` model field) is set to maximize flux through the generic human biomass reaction (`MAR13082`), and all exchange reactions are open.
```matlab
ihuman.rxns(ihuman.c == 1)

% ans =
% 
%   1×1 cell array
% 
%     {'MAR13082'}
```


## Run FBA

Run FBA using the RAVEN `solveLP` function.
```matlab
sol = solveLP(ihuman)

% sol = 
% 
%   struct with fields:
% 
%        x: [13070×1 double]
%        f: -187.3536
%     stat: 1
%      msg: 'Optimal solution found'
```

The `sol.f` field contains the (negative) value of the objective, and the `sol.x` vector contains the flux value for each reaction.

!!! important
	As mentioned, all the exchange reactions are fully opened (upper and lower bounds set to 1000 and -1000, respectively). Therefore, the value of the biomass flux here is meaningless (except that it is nonzero), and its units are undefined. Additional constraints, such as those defining the flux bounds of exchange reactions, are necessary to define a feasible solution space.



## An FBA example

#### Calculation of ATP yield

To illustrate an example of a more meaningful flux solution, we can use FBA to calculate ATP yield (more specifically, the amount of ADP phosphorylated) per glucose consumed. 

ATP yield can be quantified by the amount of flux through the ATP hydrolysis reaction `MAR03964`.
```matlab
constructEquations(ihuman, 'MAR03964')

% ans =
% 
%   1×1 cell array
% 
%     {'ATP[c] + H2O[c] => ADP[c] + H+[c] + Pi[c]'}
```

Change the objective to maximize flux through the ATP hydrolysis reaction:
```matlab
ihuman = setParam(ihuman, 'obj', 'MAR03964', 1);
```

Prevent import of all metabolites except glucose, for which the max import flux is set to 1 (mmol/gDW/h):
```matlab
ihuman = setExchangeBounds(ihuman, 'glucose', -1);  % negative flux indicates import
```

!!! note
	By default, the `setExchangeBounds` function allows unrestricted *export* of all metabolites. The *import* of all metabolites is blocked, except for the metabolites specified in the input argument.


Now perform FBA to determine the maximum amount of ATP hydrolyzed (ADP phosphorylated) per equivalent of glucose consumed.
```matlab
sol = solveLP(ihuman)

% sol = 
% 
%   struct with fields:
% 
%        x: [13070×1 double]
%        f: -2
%     stat: 1
%      msg: 'Optimal solution found'
```

As expected, the theoretical mol ADP phosphorylated per mol glucose consumed is 2. Note that we have not allowed the import of oxygen, so this is under anaerobic conditions. This is of course purely theoretical given that most humans produce very little of anything when deprived of oxygen.

Let us now calculate the same ATP yield, but in the present of oxygen. We first need to update the exchange constraints:
```matlab
ihuman = setExchangeBounds(ihuman, {'glucose', 'O2'}, [-1, -1000]);
```

Note that we are allowing effectively infinite oxygen consumption, since we are interested in non-O2-limited ATP yield per glucose. Now re-run the FBA to see how the maximum yield has changed:
```matlab
sol = solveLP(ihuman)

% sol = 
% 
%   struct with fields:
% 
%        x: [13070×1 double]
%        f: -31.5000
%     stat: 1
%      msg: 'Optimal solution found'
```

We now see a much higher ATP yield per glucose consumed of 31.5.






