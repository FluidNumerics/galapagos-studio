# Submit Interactive Jobs
You can reserve time in an interactive session on a compute node using `salloc`

For all interactive workflows, you should be aware that you are charged for each second of allocated compute resources. It is best practice to set a wall-time when allocating resources. This helps avoid situations where you will be billed for idle resources you have reserved.

## Start a bash shell on a compute node
The easiest way to start an interactive session on a compute node is to use Slurm's `salloc` command to start a shell. 

As an example, you can reserve 1 hour of exclusive access to a single node in the default slurm partition.
```
salloc --time=1:00:00 -N1 -c64 --mem=226G --gres=gpu:mi210:4 --exclusive 
```
In this example, the `-N1` flag reserves a whole node for a single task with 64 cores per task (`-c64`) and all available memory for our MI210 node (`noether`). The `--exclusive` flag ensures you have exclusive access to the node. Once your job allocation has been granted, you will be logged in to the compute node that best fits your job allocation parameters.

!!! note
    You will need to replace the argument to the `--account` flag with your Slurm account.


