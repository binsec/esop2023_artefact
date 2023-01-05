How to replay sections 7.5 and 8 experiments?
===


Experiments of sections 7.5 and the case study section 8 only need a single run per benchmark with precise configuration. A configuration file is provided alongside the benchmarks to reproduce the results.

*NB* those need to be run in their respective benchmark folder.

# Section 7.5

## RSA

For exploitability ground truth, we refer the reader to the [benchmark description](./benchmarks.md) or the paper itself.

To run the RSA with a single reset fault:
- the `CRT-RSA_aumuller` version will not finish after 10 days. The script for the full version is the following.
```
cd /home/binsec-ase/artefact/benchmarks/CRT-RSA_aumuller/
binsec -config 7.5_1reset.cfg 
```
For a partial and faster run, you can add a 1 second timeout on the solving time per query, and a total timeout at 1 hour.
```
timeout 3600 binsec -config 7.5_1reset_TOsolver.cfg 
```

- the `CRT-RSA_shamir` version also does not finish after 10 days. The script for the full version is the following.
```
cd /home/binsec-ase/artefact/benchmarks/CRT-RSA_shamir/
binsec -config 7.5_1reset.cfg 
```
For a partial and faster run, you can add a 1 second timeout on the solving time per query, and a total timeout at 1 hour.
```
timeout 3600 binsec -config 7.5_1reset_TOsolver.cfg 
```

- the `CRT-RSA_basic` can be also quite long.
```
cd /home/binsec-ase/artefact/benchmarks/CRT-RSA_basic/
binsec -config 7.5_1reset.cfg 
```
We can stop at the first attack found, which takes about 3s.
```
binsec -config 7.5_1reset_stopFirst.cfg 
```

## Secret keeping machine

The scripts to replay 1 bit-flip on the two version of the secret keeping machine:
- the `dullien_array` implementation
```
cd /home/binsec-ase/artefact/benchmarks/dullien_array/
binsec -config 7.5_1bitflip.cfg 
```
\\ How long ? (IP) too long -> make faster version

- the `dullien_linked` implementation, which takes a long time to run.
```
cd /home/binsec-ase/artefact/benchmarks/dullien_linked/
binsec -config 7.5_1bitflip.cfg 
```
Here is a shorter version stopping at the first attack found (\\ TODO s).
```
binsec -config 7.5_1bitflip_stopFirst.cfg
```
\\ TODO crash


## Secswift protection

To run a 1 test inversion attack on the `VerifyPIN_0` protected with the SecSwift countermeasure, use the following command (takes less than 1s).
```
cd /home/binsec-ase/artefact/benchmarks/VerifyPIN_0_ss/
binsec -config 7.5_1TI.cfg 
```


# Case study (section 8)

In the original WooKey program, `WooKey_original`, we study two sections in particular (section 1 takes about 1h, and section 2 takes around 3min).
```
cd /home/binsec-ase/artefact/benchmarks/WooKey_original/
binsec -config 8_section1.cfg
binsec -config 8_section2.cfg
``` 
A faster version of the analysis of section 1 uses test inversion instead of arbitrary data faults (which are equivalent in the attack paths found for this particular benchmark). It takes about 30s to run.
```
binsec -config 8_section1_fast.cfg
``` 

To run the same experiments on the version proposed by Lacombe *et al.*, `WooKey_Lacombe`, use the following commands (section 1 takes about 1h30, and section 2 takes around 2min). 
```
cd /home/binsec-ase/artefact/benchmarks/WooKey_Lacombe/
binsec -config 8_section1.cfg
binsec -config 8_section2.cfg
``` 
A faster version of the analysis of section 1 uses test inversion instead of arbitrary data faults (which are equivalent in the attack paths found for this particular benchmark). It takes about 20min to run.
```
binsec -config 8_section1_fast.cfg
```

To test our protected version, use the following commands (section 1 takes about 1h30, and section 2 takes around 2min).
```
cd /home/binsec-ase/artefact/benchmarks/WooKey_protected/
binsec -config 8_section1.cfg
binsec -config 8_section2.cfg
``` 
A faster version of the analysis of section 1 uses test inversion instead of arbitrary data faults (which are equivalent in the attack paths found for this particular benchmark). It takes about 30s to run.
```
binsec -config 8_section1_fast.cfg
```


# Other attacks on WooKey (section 8)

In the paper, two other attacks are explored.

The first one, `WooKey_ISO` can be reproduced with the following command. It takes around \\ TODO to run.
```
cd /home/binsec-ase/artefact/benchmarks/WooKey_ISO/
binsec -config 8_attack.cfg
``` 

The second attack, `WooKey_TI` can be reproduced with the following command. It takes under 30s to run.
```
cd /home/binsec-ase/artefact/benchmarks/WooKey_TI/
binsec -config 8_attack.cfg
``` 