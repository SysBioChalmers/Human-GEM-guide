# Flux Balance Analysis

One of the most common analysis methods for GEMs is flux balance analysis (FBA). This page will briefly walk through the basics of using FBA with Human-GEM.

Examples are given for both **MATLAB** (using the RAVEN Toolbox) and **Python** (using COBRApy); select the corresponding tab in each code block. The examples assume the model has already been loaded, as described in [Getting started](getting_started.md) — as `humanGEM` in MATLAB and as `model` in Python.

!!! important
	You must have a linear optimization solver (e.g., Gurobi) installed and accessible to run FBA. See the [installation](installation.md) page of this guide or the [RAVEN instructions](https://github.com/SysBioChalmers/RAVEN/wiki/Installation#dependencies) for details on setting up a solver.

!!! note "Python tooling"
    The Python examples on this page use [COBRApy](https://opencobra.github.io/cobrapy/), which is a stable, released package (`pip install cobra`). Other pages use the packages [`raven-toolbox`](https://github.com/SysBioChalmers/raven-toolbox) and [`geckopy`](https://github.com/SysBioChalmers/geckopy), which are under active development and installed from GitHub `main`.


## Optimization objective

By default, the model objective (defined by the `.c` model field) is set to maximize flux through the generic human biomass reaction (`MAR13082`), and all exchange reactions are open.

=== "MATLAB"
	```matlab
	humanGEM.rxns(humanGEM.c == 1)

	% ans =
	% 
	%   1×1 cell array
	% 
	%     {'MAR13082'}
	```

=== "Python"
	```python
	[r.id for r in model.reactions if r.objective_coefficient != 0]

	# ['MAR13082']
	```


## Run FBA

=== "MATLAB"
	Run FBA using the RAVEN `solveLP` function.
	```matlab
	sol = solveLP(humanGEM)

	% sol = 
	% 
	%   struct with fields:
	% 
	%          x: [12931×1 double]
	%          f: 124.8681
	%       stat: 1
	%        msg: 'Optimal solution found'
	%     sPrice: [8461×1 double]
	%      rCost: [12931×1 double]
	```

	The `sol.f` field contains the value of the objective, and the `sol.x` vector contains the flux value for each reaction.

=== "Python"
	Run FBA using the COBRApy `optimize` method.
	```python
	sol = model.optimize()
	sol.objective_value

	# 124.8681
	```

	The `objective_value` gives the value of the objective, and `sol.fluxes` contains the flux value for each reaction.

!!! important
	As mentioned, all the exchange reactions are fully opened (upper and lower bounds set to 1000 and -1000, respectively). Therefore, the value of the biomass flux here is meaningless (except that it is nonzero), and its units are undefined. Additional constraints, such as those defining the flux bounds of exchange reactions, are necessary to define a feasible solution space.



## An FBA example

#### Calculation of ATP yield

To illustrate an example of a more meaningful flux solution, we can use FBA to calculate ATP yield (more specifically, the amount of ADP phosphorylated) per glucose consumed. 

ATP yield can be quantified by the amount of flux through the ATP hydrolysis reaction `MAR03964`.

=== "MATLAB"
	```matlab
	constructEquations(humanGEM, 'MAR03964')

	% ans =
	% 
	%   1×1 cell array
	% 
	%     {'ATP[c] + H2O[c] => ADP[c] + H+[c] + Pi[c]'}
	```

=== "Python"
	```python
	model.reactions.get_by_id('MAR03964').build_reaction_string(use_metabolite_names=True)

	# 'ATP + H2O --> ADP + H+ + Pi'
	```

Change the objective to maximize flux through the ATP hydrolysis reaction:

=== "MATLAB"
	```matlab
	humanGEM = setParam(humanGEM, 'obj', 'MAR03964', 1);
	```

=== "Python"
	```python
	model.objective = 'MAR03964'
	```

Prevent import of all metabolites except glucose, for which the max import flux is set to 1 (mmol/gDW/h):

=== "MATLAB"
	```matlab
	humanGEM = setExchangeBounds(humanGEM, 'glucose', -1);  % negative flux indicates import
	```

	!!! note
		By default, the `setExchangeBounds` function allows unrestricted *export* of all metabolites. The *import* of all metabolites is blocked, except for the metabolites specified in the input argument.

=== "Python"
	```python
	# block import of all metabolites (set exchange lower bounds to 0), then allow glucose import
	for r in model.exchanges:
	    r.lower_bound = 0
	model.reactions.MAR09034.lower_bound = -1   # MAR09034 is the glucose exchange reaction
	```

	!!! note
		Setting each exchange reaction's lower bound to 0 blocks *import* while leaving *export* unrestricted (upper bound 1000), which reproduces the behaviour of the RAVEN `setExchangeBounds` function.


Now perform FBA to determine the maximum amount of ATP hydrolyzed (ADP phosphorylated) per equivalent of glucose consumed.

=== "MATLAB"
	```matlab
	sol = solveLP(humanGEM)

	% sol = 
	% 
	%   struct with fields:
	% 
	%          x: [12931×1 double]
	%          f: 2
	%       stat: 1
	%        msg: 'Optimal solution found'
	%     sPrice: [8461×1 double]
	%      rCost: [12931×1 double]
	```

=== "Python"
	```python
	model.optimize().objective_value

	# 2.0
	```

As expected, the theoretical mol ADP phosphorylated per mol glucose consumed is 2. Note that we have not allowed the import of oxygen, so this is under anaerobic conditions. This is of course purely theoretical given that most humans produce very little of anything when deprived of oxygen.

Let us now calculate the same ATP yield, but in the presence of oxygen. We first need to update the exchange constraints:

=== "MATLAB"
	```matlab
	humanGEM = setExchangeBounds(humanGEM, {'glucose', 'O2'}, [-1, -1000]);
	```

=== "Python"
	```python
	model.reactions.MAR09048.lower_bound = -1000   # MAR09048 is the O2 exchange reaction
	```

Note that we are allowing effectively infinite oxygen consumption, since we are interested in non-O2-limited ATP yield per glucose. Now re-run the FBA to see how the maximum yield has changed:

=== "MATLAB"
	```matlab
	sol = solveLP(humanGEM)

	% sol = 
	% 
	%   struct with fields:
	% 
	%          x: [12931×1 double]
	%          f: 31.5000
	%       stat: 1
	%        msg: 'Optimal solution found'
	%     sPrice: [8461×1 double]
	%      rCost: [12931×1 double]
	```

=== "Python"
	```python
	model.optimize().objective_value

	# 31.5
	```

We now see a much higher ATP yield per glucose consumed of 31.5.
