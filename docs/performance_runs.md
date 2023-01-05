How to script BINSEC/ASE for multiple runs to reproduce the paper's experiments?
===

# Architecture

Python scripts are in the `multirun-script` folder. The main script is `mutlirun.py`. It expects two arguments: 
- the name of the output directory, that it will create if new.
- the name of the directory containing the configuration scripts `fault_config` and `bench_config`.

It will output the logs produced by BINSEC/ASE and a csv file summing up essential metrics.

***WARNING*** multirun.py is to be launched from the `multirun-script` folder for correct paths.
```
cd /home/binsec-ase/artefact/multirun-script
```


# Performance runs

***WARNING*** by default, each multi-run will take 1/3 of your CPU cores, it can be changed in the `fault_config` files.

Few configurations are provided.

- `7.1_no_fault` runs the benchmarks without fault to get the data from Table 2 "Benchmarks characteristics and statistics of a standard SE analysis". Expected runtime on 4 CPU cores: 8 min.
```
python3 multirun.py check_7.1 7.1_no_fault/
```

- `7.2_exploitability` runs the experiments necessary to get section 7.2 results. Expected runtime on 4 CPU cores: 1 min.
```
python3 multirun.py check_7.2 7.2_exploitability/
```

- `7.3_scalability` runs the experiments for section 7.3. A fast `7.3_scalability_fast` version is provided to reduce computation time, the full version of the experiment `7.3_scalability_long` takes a few days with 40 CPU cores. Note that you will not get the exact paper results with the fast version, in particular, it does not go above 3 faults with a reduced timeout (10min instead of 24h). Expected runtime for **fast** version on 4 CPU cores: 55min.
```
python3 multirun.py check_7.3 7.3_scalability_fast/
```

- `7.4_optimizations` runs the experiments for section 7.4. Like before, a fast `7.4_optimizations_fast` version is provided to reduce computation time, the full version of the experiment `7.4_optimizations_long` takes a few days with 40 CPU cores. Note that you will not get the exact paper results with the fast version, in particular, it does not go above 4 faults with a reduced timeout (1h instead of 24h). Expected runtime for **fast** version on 4 CPU cores: 1h.
```
python3 multirun.py check_7.4 7.4_optimizations_fast/
```

- `test_try` contains everything commented so you can play with it. All parameter names should be self-explanatory with the rest of the provided documentation in mind.
```
python3 multirun.py test_some_stuff test_try/
```
