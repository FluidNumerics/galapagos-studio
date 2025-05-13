# Software on Galapagos

Fluid Numerics provides pre-installed compilers and software on each of our compute nodes through [lmod environment modules](https://lmod.readthedocs.io/en/latest/). Because the Galapagos is heterogeneous, we maintain separate software builds for each compute nodes. Generally, we aim to have the same software available across all systems, but in some cases it doesn't make sense (we won't put CUDA on our AMD GPU accelerated nodes for instance.) 


## Listing available software
When on a compute node, you can use `module avail` to list the available software. Initially, this will display software installed with the core system compilers and system package manager.

## Loading software
Loading a software stack starts with loading a compiler. Once a compiler is loaded, `module avail` will then reveal all of the software built with that compiler.

