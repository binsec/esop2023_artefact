BINSEC/ASE configuration
===

BINSEC/ASE can be used directly in command line. All options can be written directly in the command line, or can be written in a configuration file. As the latter option is more human-readable and easier to iterate over, it will be the one chosen in this manual.
Each benchmark is accompanied by a `config.cfg` file. We will detail its content below.

Note that configuration lines can be in any order provided they are in the right section. In practice, we included all of them and commented those not useful for each benchmark.


# Kernel options
The following section tells BINSEC/ASE which `isa` the executable has been built for. Here we focus on Intel x86 architecture for convenience. 
The `entrypoint` defines where the symbolic engine should start running, usually the main function. It can be the symbol of another function for a per-function analysis.
Finally, `file` is the name of the executable to analyze.
```
[kernel]
isa = x86
entrypoint = main
file = verifyPIN_0.x
```

# Symbolic execution options
The next section defines the symbolic engine options.
```
[sse]
enabled = true
memory = mem
depth = 200
goal-address = 0x08049ed8
assert-fail-address = 0x08049ebc
```
You need the `enabled` option at true to launch the SE engine. 
The `memory` option refers to the memory file used to give set-up information to the engine. A `mem` file is provided with each benchmark. It is usually used to give an initial value to the stack pointer `esp`, and for the `gs` pointer when it is used in the binary. If the program's data is not initialized from the `entrypoint`, we would need to add it here to set up specific values, they will be considered symbolic otherwise.
The `depth` option sets the maximum depth a path will be analyzed for. This provides some control over the analysis termination.
The `goal-address` is the address in the binary you wish to reach, and find inputs (and a fault sequence) for. The analysis of the path reaching the goal address will be cut after the reach. It takes only one address. `assert-fail-address` is the address where you know you won't reach your goal from. In the provided benchmark, we use an `assert` in the C code to evaluate the oracle, hence the assert `true` branch is our goal address and the `false` branch is our failure address.

## Additional SE options

Additional SE directives can be set directly with BINSEC/SAE `directives` option. The form is provided below, with an address followed by the directive to apply there. The `\` after the `=` is mandatory to break the line. Then each line ends with a `;\` except the last one. The order of the directives are significant.
```
directives = \ 
    0x0804a04e reach * ;\
    0x0804a04e cut ;\
    0x08049e39 assume (non_det<32> <u 15<32>)
```
In particular, it can be useful to set an additional `cut` if there are error-handling processes we know we won't reach our goal from. If there are multiple addresses leading to success, a `reach` can be added (followed by a cut). Note that to make sure to find all paths reaching a `reach` address, use the syntax `reach *`. If an extra assumption is required, it is specified with an `assmue` followed by the condition in Dba syntax.


By default, the exploration will be exhaustive and follow all program path up to `depth` instructions deep. If you wish to stop the analysis at the first path reaching you goal, add the following option.
```
single-solution = true
```
We provide an option to compute the complexity of queries in terms of the number of `ite` operators, providing the minimal, average and maximal number of `ite` in queries. However, this extra computation slows down significantly the analysis and is thus deactivated by default, to activate it, add the following line.
```
compute-query-complexity = true
```

# Fault injection options
Fault injection options are written in the `[sse]` section.
The address range the injection process can target is specified by the `target-addresses` option. It expects one or more address ranges, with only a ',' separator.  
```
target-addresses = 0x08049ddf,0x08049e4c,0x08049d88,0x08049dc5
```
The maximum number of faults allowed tpo the attacker model is specified by the `max-faults` option. Its default value is 0. Please note that attack path will have at most max-faults faults, so some paths may have less, or not be minimal in their number of faults.
```
max-faults = 1
```
The attacker actions are restricted to the following fault models, specified by the `fault-model` option: 
- `None`
- `ArbitraryData` which is the same as `ArbitraryDataIte`, `ArbitraryDataMul`, `ArbitraryDataAnd`, `ArbitraryDataXor` and `ArbitraryDataBranching`
- `ResetBranchless` which is the same as `ResetBranchlessIte`, `ResetBranchlessSub`, `ResetBranchlessAnd`, `ResetBranchlessXor` and `ResetBranching`
- `BitFlipBranchless` and `BitFlipBranching`
- `TestInversionBranchlessIte` and `TestInversionBranching`

For instance:
```
fault-model = ArbitraryData
``` 

Next goes the `injection-method`. When using a test inversion fault model, you need to use the following option. No optimization is available for those fault models.
```
injection-method = test-inversion
```
For the other fault models, you can choose between a 'normal' injection process, labeled `always`, or the Injection On Demand (IOD) optimization.
```
injection-method = on-demand
```
To activate the Early Detection of fault Saturation (EDS) optimization, add the following option.
```
at-branch-optim = true
```

Then, specifying where the analysis will check that the number of fault in the path does not exceed max-faults is written:
```
where-check-nb-faults = branch
```
There are 3 different values: 
- `loc` stands for at each fault location. This is recommended for branching fault models.
- `branch` to check at each conditional branch. This is recommended for branchless models.
- `end` to check only when a path reached the attacker goal. This is not recommended as it follows many more paths that are pruned early otherwise.

To prevent faulting some dba variables, the following option is used.
```
target-blacklist = esp
```
In practice, we use it to prevent faults to the `esp` register as it does not represent a supported fault model in the sense that it is a true challenge for the solver. In some programs, the `gs` register is also used to refer to memory regions within which data is fetched, we recommend blacklisting it too to avoid attack paths setting uninitialized memory exactly right. 

By default, faults on addresses are not performed, as we do not have an efficient algorithm for them. If you which to activate faults on addresses anyway, add the following option.
```
fault-vars-in-adresses = true
```

By default, faults are performed only on registers and memory data, not on flags. To add this option, include the following line.
```
fault-flags = true
```

# Solver options

If you which to use another solver than the default bitwuzla with native binding, add this next line in the `[sse]` section.
```
no-native-solver = true
```
Then, add the following section, where you can choose the `solver` you wish to use: `bitwuzla` without the native binding, `boolector` or `z3`.
```
[fml]
solver = z3
```
If you wish to set a timeout per query to the solver to speed up the analysis but loose completeness, you may add the following option taking a integer, seconds are the unit. Note that you can't use the native binding with this option.
```
solver-timeout = 5
```

# Output Dba of the program

To see how a binary in transformed into Dba by BINSEC/ASE, on a per function basis, use the following command line. The `entrypoint` is the address of the target function.
```
binsec disasm -isa x86 -entrypoint 0x08048623
```
Some stuff will appear on the terminal, though the Dba will be written in a `out.dba` file.
