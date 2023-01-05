How to setup a new program for BINSEC/ASE analysis
===

Configuration files are detailed in `configuration.md`, and options are supposed to be known in this tutorial.

Consider an existing configuration file as a basis to start this new one. 

# Oracle

The attacker goal, also called oracle, is usually an assertion over programs variables in C, for simplicity reasons. This requires modification of the source code before compilation.

It is also possible to add a BINSEC directive natively. This is harder to write for a user new to BINSEC configuration syntaxe.

# Compilation

We use the command line ```gcc -Wall -m32 -static -O0 -g source.c -o source.x```.
- `-m32` flag is to specify a target architecture 32 bits (Intel). Other architecture can be targeted but with less support. This comes with newer version of BINSEC. 
- `-static` flag includes all library functions statically to the executable. BINSEC does not support dynamic linking.
- `-O0` flag is recommended for convenience to compile without any compile optimization, which can change the program behavior, removing redundant tests for instance. It also improves the readability of the assembly.
- `-g` flag is recommended for convenience as it enables debug information making it easier to compare assembly code and C code for reasoning on results.

Other compilers can be used as well, as long as necessary behavior flags are used.

# Configuration

To configure BINSEC/ASE, you need to open the target binary with your favorite disassembly software, and look for several informations.
- where do you want your symbolic execution to start (`entrypoint` option) ? It is usually at `main` in practice.
- where is your oracle ? You need the code line addresses of a success and of a failure for `goal-address` and `assert-fail-address` options respectively.
- You may need to load data sections to ensures the initialization of global variables with the SE option `-sse-load-sections .data,.rodata,.bss`, we recommend not doing it if unnecessary as it may also lead to incorrect output. You may also add variables in the memory file to init the SE.

At this stage we recommend a BINSEC/ASE run without any faults, with the `fault-model = None` option. There are two things to check:
1. Is the output correct ? In particular, do you have errors or do you reach your oracle when you shouldn't without faults ?  
2. Is the `depth` enough ? The number of cut path is in the output, if you have some, then consider increasing the depth until your exploration is complete. The depth of a 'normal' SE run is used as a basis for limiting faulted path.

BINSEC/ASE does not handle system calls, you may have to stub then in the configuration. Here is an example transforming 3 function calls into virtual NOPs.
```
[disasm]
decode-replacement = \
0x080517a0 ->     0: esp<32> := esp<32> + 4<32>; goto 1 \
                     1: goto @[esp<32> - 4<32>,4] \
0x08058900 ->     0: esp<32> := esp<32> + 4<32>; goto 1 \
                     1: goto @[esp<32> - 4<32>,4] \
0x08058ab0 ->     0: esp<32> := esp<32> + 4<32>; goto 1 \
                     1: goto @[esp<32> - 4<32>,4] \ 
```

Now that our program is set for a classic SE run, we can play with adding an attacker model and see what happens.
- where is the code you want to fault ? You need to select one or multiple intervals of addresses that will be faulted to fill the `target-addresses` option. We recommend to have one injection target interval per function and omit the beginning pushing the return value on the stack. 
- what fault model to use ? What type of faults to inject, how many at most ? 
You can also ask to fault flags (which are not faulted by default), or to fault values serving as addresses (which are not by default as support for faults on addresses is not efficient, making the solver or the SE explodes).
You can chose whether to have fault checks (number of fault lesser or equal to the maximum allowed, and fault constraints satisfied) at each fault location (good for branching models), at each branch (good for forkless models), or at the end.
- you can set whether to stop the exploration at the first attack found, or explore everything.

Then, start by adding one fault, and manually verify each result against assembly and C code to make sure the attacks found make sense. A bad configuration, especially an issue with initialization will yield incoherent results. Also, manually check all dynamic jumps that appear, you may have set the injection target to wide, or use a mode confusing the SE engine. Ideally, no dynamic jumps should be left.

You may have to cut path exploration beyond a certain address, in error handling in particular, which is to add in the configuration as `directives`.

If you still have incorrect results, you can glimpse into BINSEC/ASE inner workings by displaying internal logs. The level goes from 0 (none) to 10 (everything). This is a `[sse]` option and can be written in the configuration file, but we found it easier to add it to the command line and to pipe the result in a file for examination as it can be very long depending on the level.
```
binsec -config config.cfg -sse-debug-level 3 > log.txt
```
Also, do not hesitate to open an issue or contact the authors.

# Experiments

Once a benchmark setup, it is easy to change the fault model to check the vulnerability of a program to various attacker models. It can be easily scripted as well.
If you wish to change the oracle, if it is at C level, you need to create a new binary and start the configuration process once again.


