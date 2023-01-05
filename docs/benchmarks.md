Benchmarks
===

All benchmarks are in `benchmraks`
```
cd /home/binsec-ase/artefact/benchmarks
```
in a folder with their name, which contains the source code in C `.c`, the binary executable `.x`, the configuration file `.cfg` and the memory file `mem`.

# Performance benchmarks (section 7.1 to 7.4)

There are 8 versions of the VerifyPIN program, taken from FISSC [[1]](#bibliography), `VerifyPIN_0` to `VerifyPIN_7`. They are vulnerable to test inversion up until `VerifyPIN_4` while the following versions are resistant to it. They are all vulnerable to arbitrary data faults.

We also made 2 unrolled version inspired by `VerifyPIN_0`, one with a 4 digits pins, `VerifyPIN4`, and one with a 16 digits pin, `VerifyPIN16`.

The last two performance benchmarks are the npo2 programs [[2]](#bibliography). The first one, `npo2_c1` is vulnerable to 1 arbitrary data fault while the second, `npo2_c2` is resistant to it.


# Other benchmarks (section 7.5)

## SecSwift protection

We applied the secswfit protection to `VerifyPIN_0` and call it `VerifyPIN_0_ss`.

You may see some warnings appear during the analysis, they do not compromise the attack path found. 

**Warning** this benchmark is quite long to run (~35 min for 1 ArbitraryData branchless fault ).

## RSAs

We have 3 versions of RSA with various protections, taken from FISSC [[1]](#bibliography).

The first, `CRT-RSA_basic`, version is vulnerable to a reset.
The second version, `CRT-RSA_shamir`, implements the protections from Shamir's work, and is also vulnerable to one reset fault.
The last version, `CRT-RSA_aumuller`, implements Aumuller protection ans is said to be resistant to one reset fault.

**Warning** those benchmarks can be very long to run. The basic version less so.

## Dullien's secret keeping machine

We have the linked-list and the array implementations of the secret keeping machine program [[3]](#bibliography), called `dullien_array` and `dullien_linked`. Those require the activation of `fault-vars-in-adresses` option. The `dullien_array` version is resistant to one arbitrary data fault in memory (note that our analysis will find attack paths, all faulting 'register' values). The second version, `dullien_linked` is vulnerable to one arbitrary data fault.

**Warning** Those benchmarks are long to run (~1h for branchless 1 fault).


# WooKey case study (section 8)

The orginal WooKey code, as stubbed in Lacombe's work [[4]](#bibliography) is called `WooKey_original`. It is vulnerable to 1 arbitrary data fault, which has equivalent effects in attack paths found than test inversions, which are much faster to run.

The second version is the patch proposed by Lacombe et al, called `WooKey_Lacombe`. Where there remain one attack path for an arbitrary data fault.

The last version is our proposed patch, `WooKey_protected` which remove the identified attack paths in the part of the code under analysis.

**Warning** Those benchmarks are long to run.

# Other attacks on WooKey (section 8)

We replay the attack found in [[4]](#bibliography) on WooKey's iso8716 library, and call this benchmark `WooKey_ISO`. It is vulnerable to 1 arbitrary data fault.

We replay the attack on a wrongly implemented test duplication protection [[5]](#bibliography) `WooKey_TI`, using faults on flags with the test inversion model, or a normal arbitrary data fault.

# Bibliography

[1] Dureuil, Louis, et al. "FISSC: A fault injection and simulation secure collection." International Conference on Computer Safety, Reliability, and Security. Springer, Cham, 2016.

[2] Le, Hoang M., et al. "Resilience evaluation via symbolic fault injection on intermediate code." 2018 Design, Automation & Test in Europe Conference & Exhibition (DATE). IEEE, 2018.

[3] Dullien, Thomas. "Weird machines, exploitability, and provable unexploitability." IEEE Transactions on Emerging Topics in Computing 8.2 (2017).

[4] Lacombe, Guilhem, et al. "Combining Static Analysis and Dynamic Symbolic Execution in a Toolchain to detect Fault Injection Vulnerabilities." PROOFS WORKSHOP (SECURITY PROOFS FOR EMBEDDED SYSTEMS). 2021.

[5] Martin, Thibault, Nikolai Kosmatov, and Virgile Prevosto. "Verifying redundant-check based countermeasures: a case study." Proceedings of the 37th ACM/SIGAPP Symposium on Applied Computing. 2022.