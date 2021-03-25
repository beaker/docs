
# Beaker Interactive

Beaker Interactive is a way to use Beaker to quickly experiment
without the overhead of creating an image each time you change your code.

## Select a Machine

Beaker Interactive is available on any machine that is running Beaker.
You can see a list of available machines by first selecting a cluster
from the [list of on-premise clusters](https://beaker.org/clusters).
Once you have a cluster, select a node within that cluster and get its hostname.

## Connect to the Machine

Connect to the machine via SSH. You will need to be on the VPN.
If you have any issues connecting to the machine, contact IT.

## Start a Session

Once on the machine, run `beaker session create` to start a new session.
This will ask for your user token which is used to authenticate with Beaker.
Beaker will then claim resources on the node, which may take a while if the node is full.
Finally, Beaker will create an interactive session for you.

## Interactive Environment

Each interactive session is backed by a Docker container.
Within the container, you are free to do whatever you want; you cannot affect other processes on the machine.
Everything except for your home directory and `/net` is destroyed once the session stops.

By default, interactive sessions use the `allenai/base:cuda11.2-ubuntu20.04` Docker image which is maintained by the Beaker team.
This image is based on Ubuntu 20.04 and already has CUDA 11.2 drivers installed.
It also comes with some useful tools like the AWS CLI, Google Cloud CLI, and the Beaker CLI.
If you find that any tools are missing, please contact the Beaker team and we will consider adding them.

Your home directory is mounted into the container so anything you write to `~` will persist between sessions.
NFS is also available in the `/net` directory.

### Additional GPUs

By default, sessions are assigned 1 GPU.
If you need more GPUs, use the `--gpus <count>` flag when creating a session e.g. `beaker session create --gpus 2`.

There is currently no way to run an interactive session without claiming a GPU.
If you would like to use Beaker Interactive without a GPU, please contact the Beaker team.
This is a feature that we will build if there is sufficient demand.

### Running a Command

By default, interactive sessions run a Bash shell.
If you would like to run a command instead, you can pass additonal arguments when creating a session
and they will be passed along to the Docker container.

For example, the command below prints the Python version.
You could also use this to run a Python script in your home directory.

```
beaker session create -- python3 --version
```

### Alternative Base Images

If you need to use a different base image, pass the `--image` flag when creating a session.

For example, this command uses the AllenNLP base image and runs the `test-install` command.

```
beaker session create --image allennlp/allennlp -- allennlp test-install
```
