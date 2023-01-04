BINSEC/ASE overview
===

# Installation procedure

The VM or docker should already be installed, see the [getting started guide](getting_started.md) otherwise.

All the commands are the same for VM and docker.

# General architecture

- `binsec-ase` contains the BINSEC/ASE executable.

- `docs` contains tutorials and further documentation:
    - [benchmarks.md](docs/benchmarks.md) details the available benchmarks.
    - [configuration.md](docs/configuration.md) details BINSEC/ASE configuration files linked with each benchmark.
    - [output.md](docs/output.md) explains BINSEC/ASE output.
    - [performance_runs.md](docs/performance_runs.md) explains how to launch **experiments from sections 7.1 to 7.4**, and the usage of our multi-run script parallelizing tasks.
    - [attack_runs.md](docs/attack_runs.md) details how to **reproduce attacks from sections 7.5 and 8**.
    - [plot_graphs.md](docs/plot_graphs.md) details how to make plots and in particular those present in the paper.
    - [new_setup.md](docs/new_setup.md) provides information to setup a new benchmark.

- `benchmarks` contains the benchmarks present in the paper per folder, with the c source code, binary executable and an example configuration file for each. Details about the various benchmarks are available in [benchmarks.md](docs/benchmarks.md).

# Available softwares

- SMT solvers: bitwuzla (native binding and external), z3 and boolector.
- dissassemblers: radare2 and cutter.

# TODO


\\ TODO major issues with dullien linked