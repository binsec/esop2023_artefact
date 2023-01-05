Getting Started Guide
===

# Installation instructions

This artefact is available as a Docker or VM for VirtualBox.

## VM installation

Import the `BINSEC-ASE` VM in VirtualBox. Start it.
The user is `binsec-ase`, and the password `binsec-ase` though it should login automatically.

The default keyboard is 'qwerty'. You can set it to 'azerty' by opening a terminal and using the command `setxkbmap fr`.

The content of the artefact is located in the `/home/binsec-ase/artefact` folder.
```
cd /home/binsec-ase/artefact
```

Note that some useful software are in `/home/binsec-ase/programs`, namely the solvers bitwuzla and boolector (z3 is also installed), the dissasembler cutter (radare2 is also installed) and the test editor vscode.

Note that vscode is installed is the VM if you wish to use it, use can simply run `code .` then.

See the `README.md` file for further documentation.

## Docker installation

Download the docker image, and launch it with
```
docker run -it --rm esop2023_artefact
```
Note that if you are using vscode, you can bind it to the docker.

The content of the artefact is located in the `/home/binsec-ase/artefact` folder.
```
cd /home/binsec-ase/artefact
```

Note that some useful software are in `/home/binsec-ase/programs`, namely the solvers bitwuzla and boolector (z3 is also installed), the dissasembler cutter (radare2 is also installed).

See the `README.md` file for further documentation.

# Paper

You will find a copy of the accepted paper in the artefact, named `draft.pdf`. The camera-ready will be uploaded when available.


# Running your first BINSEC/ASE analysis

Let's take `VerifyPIN_0` as example for this guide. You will find the source code, binary executable and configuration file in its benchmarks folder.
```
cd /home/binsec-ase/artefact/benchmarks/VerifyPIN_0
```

To run the configuration attached to a benchmark, named `verifyPIN_0.cfg`, use the following command.
```
binsec -config verifyPIN_0.cfg
```
Note that there is only one '-' before option names, instead of the most common '--'.

If you wish to display the analysis time, we suggest using the command line:
```
time binsec -config verifyPIN_0.cfg
```


The output will tell you that for this attacker model, able to perform 1 arbitrary data fault in a Forkless manner, there are 3 attack paths (`Models found`). By going up the logs, you will get the details of the inputs and fault sequence generating those attack paths.

To compare it with a run for the same attacker model using a Forking technique, run:
```
binsec -config verifyPIN_0_forking.cfg
```

While the forkless analysis explored only 6 paths, the forking one explores 41. 
This show case the main contribution of the paper, the introduction of a fault injection technique which does not induce a path explosion.

Note that some benchmarks can be quite long to run. If you wish to set a time limit, we recommend the `timeout` command. For instance, to limit the runtime to 1h (not necessary for this particular benchmark):
```
timeout 3600 time binsec -config verifyPIN_0_forking.cfg
```

See the `output.md` documentation for further information about a BINSEC/ASE output, and `configuration.md` for details about the configuration file and how to play with it to change the attacker model and the analysis options. 