# Using Jupyter Notebooks on Compute Nodes with VS Code

This tutorial assumes you are able to enter a remote VS Code session via SSH.

Suppose you have remote connected to `galapagos.fluidnumerics.cloud` and are in a remote session in VS Code. In the bottom left, there should be a blue square that says: `SSH: galapagos.fluidnumerics.cloud`.

You are connected remotely to a *login node*. In order to submit jobs to the job scheduler with the VS Code terminal, you are probably used to first entering a command like `salloc -n 1 --gpus=1 --cpus-per-task=12 --partition=gpu --constraint=mi210` to connect to a compute node. In this case, a node on the `noether` cluster. This is often sufficient; many of the user-accessible storage location (e.g., the home directory) is available to both login nodes and compute nodes. This means that you can edit the appropriate files with the VS Code interface and submit them (or even just run them locally) using the terminal. This is not always the case, however. For example, the path `/scratch` is not open to login nodes. 

You might need to interact with files in this path while you are using a Jupyter Notebook. Suppose you have a notebook with a single cell with the following lines:

```python
file = open("/scratch/garrett/buried-treasure.txt", "r")
print(file.read())
file.close()
```

Here, we are just using a kernel provided by one of our conda environments (here, the environment named `example`).

![](img/tutorial1.png?raw=true)

As you can see, since the Jupyter kernel is running on a login node, it can't "see" `/scratch`. In order to fix this, we can run a Jupyter server from noether, and then select a kernel from that server as the kernel used for the notebook. First, connect to a compute node (e.g., `noether`) using a command as mentioned above: 

```
salloc -n 1 --gpus=1 --cpus-per-task=12 --partition=gpu --constraint=mi210
``` 

At this point it is assumed you already have a conda environment set up. Run 

```
conda install jupyter
```

to install the necessary tools. Once this has installed, we can now run the server. In the same terminal that is connected to `noether`, run

```
jupyter-lab --no-browser --port=8888 --ip=0.0.0.0
```

This starts a server on the specified port. If 8888 is taken, use another (we suggest something in the 8000-9000 range). This should output something like the following:

![](img/tutorial3.png?raw=true)

Now that the server is running, you may selecte a new Jupyter kernel. In this example, this is in the top right and reads `example (Python 3.12.3)`. Click this, then `Select another kernel...`, then `Existing Jupyter server...`, then `Enter the URL of the running Jupyter server...`. In the output of the initial server launch, notice the line that reads `Or copy and paste one of these URLs:`. Copy the link under this line. (In this case, `http://noether:8080/lab?token=61066c66d995c0f24aaba0a6410267fd0a93591aab8eca08`). You need to change `noether` in this URL to `noether.armory.fluidnumerics.com` and then enter it in the prompt. It will prompt you to select a name for this kernel; we recommend leaving this blank. If this fails to connect to the kernel, you can try to enter the URL manually. 

We can now re-run our notebook and get the expected output:f

![](img/tutorial2.png?raw=true)

As we can see, our notebook is now running on a kernel which is running on `noether`. I.e., it can now access `/scratch`. 