How to read BINSEC/ASE output
===

# Exploration results

BINSEC/ASE will output a number of `[sse:result]` lines. Those may be:
- a `cut` at an address specified in the configuration, indicating the exploration reached that address and dropped that path exploration.
- a `reach` at the goal address, followed by the `Model`, i.e. the inputs and fault sequence reaching the goal address for that path. If you are in Injection On Demand mode, you will find the line 
```
[sse:result] Predicate flips:
``` 
followed by the addresses where the faulted predicate had to be taken, and `t` or `e` indicating if it was in order to taken the 'then' or 'else' branch. Finally, 
```
[sse:result] NUMBER_FAULTS = 1<32> for MAX_FAULTS = 1 
``` 
precise the number of fault used for this attack path with a reminder of the attacker fault budget.


When the exploration is over, BINSEC/ASE will output: 
```
[sse:info] Empty path worklist: halting ... 
```


# Exploration statistics

BINSEC/ASE will provide information about the path exploration of the following form.
```
[sse:info] Exploration
             Paths 6
             Paths continuing after max reached 0
             Secondary queries (EDS only) 0
             Analysed instructions 343
             Max depth instruction 200
             Max depth branchments 18
             Cut early paths 1
```
- `Path` is the number of explored paths.
- `Paths continuing after max reached` indicates how many times a technique detected that the maximum number of fault has been reached on a path.
- `Secondary queries` counts the number of `nb_fault == max` queries sent to the solver with the Early Detection of fault Saturation (EDS) optimization.
- `Analysed instructions` gives the total number of instruction analyzed (not the unique ones).
- `Max depth instruction` gives the maximal depth of analyzed path, given they are cut after the maximum depth setting value, here 200.
- `Max depth branchments` gives the maximum number of conditional jumps encountered in a path.
- `Cut early paths` provides the number of paths cut because they exceeded the maximal depth setting.

# SMT queries statistics

Then, some statistics about the SMT queries.

```
[sse:info] SMT queries
             Number of queries 99
             Number of queries sent to the solver 26
             Average solving time 0.000000
             Total assume queries 76
             Assume queries sent to solveur 26
             Total enumerate queries 23
             Enumerate queries sent to solveur 0
             Trivial true assume 24
             Trivial false assume 26
             Unknown solver 0 
```
- `Number of queries` gives the number of queries the SE engine generated in the analysis.
- `Number of queries sent to the solver` tells how many of the generated queries are actually sent to the solver.
- `Average solving time` outputs the average solving time per query in seconds
- `Total assume queries` counts how many generated queries are of the type 'assume' (there are only two types: assume and enumerate).
- `Assume queries sent to solveur` tells how many of those have actually been sent to the solver.
- `Total enumerate queries` counts how many generated queries are of the type 'enumerate'.
- `Enumerate queries sent to solveur` tells how many of those have actually been sent to the solver.
- `Trivial true assume` is how many queries of type 'assume' for true branches are arithmetically solved by the SE engine with simple recombination rules.
- `Trivial false assume` idem for false branches
- `Unknown solver` provides the number of queries the solver had an issue with, this is mostly due to solver timeout if set.

If the configuration for `compute-query-complexity` is set, more statistics will appear, detailing the minimum, maximum number of ite found in queries, the total sum as well as the number of queries and the average number of ite per query.

# Fault injection statistics

Finally, BINSEC/ASE provides some statistics about the fault injection in particular.

```
[sse:info] Fault injection
             Fault model ArbitraryData
             Injection method always
             Maximum number of faults 1
             Where number of faults checked: branch
             Where non_det constrains checked: branch
             At branch optim: true
             Injection locations 21
             Min Injection locations per path 0
             Max Injection locations per path 21
             Avg Injection locations per path 13
             Models found 3 
             Assert cut 2 
```
- `Fault model` reminds the chosen fault model.
- `Injection method` reminds the chosen injection method.
- `Maximum number of faults` reminds the maximum number of faults allowed to the attacker model.
- `Where number of faults checked` reminds of the chosen configuration for the location for checking if the number of fault exceed the maximum.
- `Where non_det constrains checked` reminds of the chosen configuration for the location for checking if internal constraints, they are checked at the same place as the number of faults in practice.
- `At branch optim` reminds of the activation of the EDS option.
- `Injection locations` gives the total number of fault location injections.
- `Min Injection locations per path`, `Max Injection locations per path`, `Avg Injection locations per path` provides more detailed statistics of the number of fault locations per path.
- `Models found` sums up the number of attack path found, detailed in the results above.
- `Assert cut` counts the number of path ending by a cut where the goal cannot be reached anymore.