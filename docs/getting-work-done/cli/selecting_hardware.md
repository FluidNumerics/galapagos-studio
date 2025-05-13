# Selecting Hardware

## Node Features & Constraints

On Fluid Numerics systems you can use [Slurm constraints](https://slurm.schedmd.com/sbatch.html#OPT_constraint) to select the compute node to run on by the features available.

You can view the available features for each partition using the `sinfo` with appropriate `--format` options. We recommend using the following :

```
sinfo --format="%10P %5c %10m %f"
```

The format statement provided to `sinfo` in the above example has the following meaning

* `%10P` - Lists the name of the partition using at most 10 characters
* `%5c`  - Lists the number of CPUs per node using at most 5 characters
* `%10m` - Lists the amount of memory per node (in MB) using at most 10 characters
* `%f`   - Lists the available features

See [`sinfo` format documentation](https://slurm.schedmd.com/sinfo.html#OPT_format) for more details.

An example listing is shown below

```
$ sinfo --format="%20N %5c %10m  %f"

NODELIST         CPUS  MEMORY     AVAIL_FEATURES
oram             24    54630       epyc,v100,hpc
nicholson        192   513791      epyc,mi300a,hpc
noether          64    240000      epyc,mi210,hpc
```

## Generic resources
On Fluid Numerics systems, we use [Generic Resources (GRES)](https://slurm.schedmd.com/gres.html) to define the GPU type and quantity available on each compute node. By specifying the GRES via the `--gres` flag, you can define the gpu type and count (per node) that you want to have access to in your job.

You can view the available features for each partition using the `sinfo` with appropriate `--format` options. We recommend using the following :

```
sinfo --format=%20N %10c %10m %20G"
```


The format statement provided to `sinfo` in the above example has the following meaning

* `%20N` - Node hostname (up to 20 characters)
* `%10c` - Number of CPUs
* `%10m` - Memory in MB
* `%20G` - GRES (Generic Resources)

An example listing is shown below

```
 sinfo --format="%20N %10c %10m %20G"
NODELIST             CPUS       MEMORY     GRES                
oram                 24         54630      gpu:v100:4          
nicholson            192        513791     gpu:mi300a:4        
noether              64         240000     gpu:mi210:4     
```

Keep in mind that on each server, some memory is set aside for the operating system

## Getting an exclusive interactive node allocation
A common workflow on the Galapagos cluster is to have a completely interactive session on a compute node with access to all CPUs, GPUs, and available memory. This section provides you a quick one-line `salloc` call that will grant you such an allocation (when resources are available), for each of our systems.

### Oram (V100)

For a single process/task on the compute node
```
salloc --time=1:00:00 -N1 -c24 --mem=45G --gres=gpu:v100:4 --exclusive
```

For one task per GPU
```
salloc --time=1:00:00 -N1 -n4 -c6 --mem=45G --gres=gpu:v100:4 --exclusive
```


### Noether (MI210)

For a single process/task on the compute node

```
salloc --time=1:00:00 -N1 -c64 --mem=226G --gres=gpu:mi210:4 --exclusive
```

For one task per GPU
```
salloc --time=1:00:00 -N1 -n4 -c16 --mem=226G --gres=gpu:mi210:4 --exclusive
```

### Nicholson (MI300A)

```
salloc --time=1:00:00 -N1 -c192 --mem=493G --gres=gpu:mi300a:4 --exclusive
```

For one task per socket
```
salloc --time=1:00:00 -N1 -n4 -c48 --mem=493G --gres=gpu:mi300a:4 --exclusive
```

## Examples

###  Selecting MI210 GPUs with constraints

This example allocates a job on a compute node with 1 MI210 GPU using Slurm constraints
```
#!/bin/bash
#SBATCH --partition=economy           
#SBATCH --gpus-per-node=1              # Request one gpu per node
#SBATCH --ntasks-per-node=1            # Indicate you are running one task per node
#SBATCH --cpus-per-task=12             # Request 12 CPUs (6 cores) for your task
#SBATCH --constraint=mi210
```

###  Selecting MI210 GPUs with GRES

This example allocates a job on a compute node with 1 MI210 GPU using GRES
```
#!/bin/bash
#SBATCH --partition=economy           
#SBATCH --gres=gpu:mi210:1             # Request one MI210 gpu per node
#SBATCH --ntasks-per-node=1            # Indicate you are running one task per node
#SBATCH --cpus-per-task=12             # Request 12 CPUs (6 cores) for your task
```


## Further Reading

* [Slurm sbatch `--constraint`](https://slurm.schedmd.com/sbatch.html#OPT_constraint)
* [Slurm sbatch `--prefer`](https://slurm.schedmd.com/sbatch.html#OPT_prefer)

