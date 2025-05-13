# Using Jupyter Notebooks on Compute Nodes with VS Code

Suppose you have a remote connection to Galapagos through VS Code.

In the bottom left corner of VS Code, you should see a blue panel that reads `SSH: galapagos`.

This indicates that you are in a VS Code session connected to Galapagos via SSH. Note that the VS Code server is running on the *login node*. In order to submit jobs to the job scheduler with the VS Code terminal, you are probably used to first entering a command like `salloc -n 1 --gpus=1 --cpus-per-task=16 --constraint=mi210 -t 4:00:00` to connect to a compute node. In this case, `noether`. This is often sufficient for accessing node-specific resources; e.g., running Python scripts that might utilize GPUs and `/scratch` directories is conventionally done through the CLI. However, it is *not* conventional to run and interact with Jupyter notebooks through the CLI.

In a local VS Code session, the user can just select their desired conda environment and proceed with running the Jupyter notebook. However, recall that the remote session is being served from the login node. VS Code does not magically "know" that your terminal is connected to a compute node and that the Jupyter notebook should use those resources. Instead, we need to run a jupyter server from the compute node.

Activate your desired conda environment, and `pip install jupyter-lab`.

Next, you need to set the environment variable `JUPYTER_DATA_DIR`. Any directory you have write permissions for is fine; `$HOME/.jupyter/data` is a typical choice. If this variable is not set, the Jupyter server will try to write files under `/tmp/run/user/<user-id>/jupyter/runtime`, which the user will not have write permissions for. You can run the following command, or add it to `.bashrc`.

```
export JUPYTER_DATA_DIR="$HOME/.jupyter/data"
```

To start the Jupyter server, run the following command:

```
jupyter-lab --no-browser --port=<port> --ip=0.0.0.0 --IdentityProvider.token=<token>
```

Here, `<token>` is an arbitrary string chosen by the user. It's essentially a password for the Jupyter server. `<port>` must be in the range of ports open on the compute node. If you are not sure which ports are open, contact your system administrator.

Now that the server is running on the compute node, you select the kernel being served at

```
http://<name of compute node>:<port>/lab?token=<token>
```

For example, the following command

```
jupyter-lab --no-browser --port=1337 --ip=0.0.0.0 --IdentityProvider.token=mycooltoken
```

allows you to use the kernel at

```
http://noether:1337/lab?token=mycooltoken
```

(Note: port `1337` will never be open.)

To select this Jupyter server, select "Select Kernel" in the top right of the VS Code tab. Then select `Select Another Kernel...` -> `Existing Jupyter Server` -> `Enter the URL of the running Jupyter Server...` and type in the URL as outlined above. VS Code should then prompt you to nickname that connection, which is then saved so you can just select it from the dropdown next time.

Now your notebook should be able to access node-specific resources.