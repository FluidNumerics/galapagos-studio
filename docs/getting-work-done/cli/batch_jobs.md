# Submit Batch Jobs

Batch jobs are useful when you have an application that can run unattended for long periods of time. You run a batch job by using the sbatch command with a batch file.

## Writing a batch file
A batch file consists of two sections

1. Batch header - Communicates settings to Slurm that specify your slurm account, the compute partition to submit the job to, the number of tasks to run, the amount of resources (cpu, gpu, and memory), and the task affinity.

2. Shell script instructions

Below is a simple example batch file:

```
#!/bin/bash
#SBATCH --job-name=example
#SBATCH --account=my-slurm-account
#SBATCH --ntasks=1
#SBATCH --ntasks-per-node=1
#SBATCH --exclusive
#SBATCH --gres=gpu:mi210:1
#SBATCH --time=00:05:00
#SBATCH --output=stdout.log
#SBATCH --error=stderr.log

hostname
```

The above batch file has multiple constraints that dictate how the job will be executed. 

* `--job-name=example`  sets the job name to `example`
* `--account=my-slurm-account` indicates that you are using the "my-slurm-account"  to log the resource usage against. **You will need to replace this with your assigned slurm account name**
* `--ntasks=1` advises the slurm controller that job steps run within the allocation will launch a maximum of 1 tasks
* `--ntasks-per-node=1` When used by itself, this constraint requests that 1 task per node be invoked. When used with --ntasks, --ntasks-per-node is treated as the maximum count of tasks per node. 
* `--gres=gpu:mi210:1` indicates that you want to use 1 MI210 GPU for this job
* `--time=00:05:00` sets a total run time of 5 minutes for job allocation. 
* `--output=stdout.log` out creates a file called `stdout.log` containing the batch script’s stdout.
* `--error=stderr.log` out creates a file called `stderr.log` containing the batch script’s stderr.

[SchedMD’s sbatch documentation](https://slurm.schedmd.com/sbatch.html) provides a more complete description of the sbatch command line interface and the available options for specifying resource requirements and task affinity.

Batch jobs are submitted using the sbatch command. 

```
sbatch example.batch
```

Once you have submitted your batch job, you can check the status of your job with the squeue command. Since the RCC is an autoscaling cluster, you may notice that your job is in a configuring (`CF`) state for some time before starting. This happens because compute nodes are created when needed to meet the compute resource demands on-the-fly. This process can take anywhere from 30s - 3 minutes.


## Changing stdout/stderr location

Slurm's `sbatch` command accepts flags for redirecting standard out and standard error that arise from commands in your batch script. By default, the standard out and standard error are redirected to `slurm-%j.out` where `%j` is replaced by the job ID. To change the output location for stdout and stderr, you can change this setting with the `-o` and `-e` flags respectively. For example


```
#SBATCH --job-name=example
#SBATCH --account=my-slurm-account
#SBATCH --ntasks=1
#SBATCH --ntasks-per-node=1
#SBATCH --exclusive
#SBATCH --gres=gpu:mi210:1
#SBATCH --time=00:05:00
#SBATCH --output=/path/to/stdout.log
#SBATCH --error=/path/to/stderr.log

hostname
```

The above batch script will send stdout to `/path/to/stdout.log` and the stderr to `/path/to/stderr.log`
