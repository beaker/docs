
# Interactive Sessions

Interactive sessions are a way to use Beaker to quickly experiment
without the overhead of creating an image each time you change your code.

## Select a Machine

Interactive sessions are available on any machine that is running Beaker.
You can see a list of available machines by first selecting a cluster
from the [list of on-premise clusters](https://beaker.org/clusters).
Once you have a chosen cluster, select any node to find its hostname.

## Connect to the Machine

Connect to the machine via SSH. You will need to be on the VPN.
If you have any issues connecting to the machine, contact IT.

## Start a Session

Once on the machine, run `beaker session create` to start a new session.
This will ask for your user token which is used to authenticate with Beaker.
Beaker will then claim resources on the node, which may take a while if the node is full.
Finally, Beaker will create an interactive session for you.

## Interactive Environment

Each interactive session is hosted in a container sandbox.  Within the container
you are free to run or install whatever you want; you won't affect other processes on the machine.
Everything except for your home directory and `/net` is destroyed once the session stops.

By default, interactive sessions use the `allenai/base:cuda11.2-ubuntu20.04` Docker image which is maintained by the Beaker team.
This image is based on Ubuntu 20.04 and already has CUDA 11.2 drivers installed.
It also comes with some useful tools like the AWS CLI, Google Cloud CLI, and the Beaker CLI.
If you find we've missed a common tool, please contact the team and we will consider adding it.

Your home directory is mounted into the container so anything you write to `~` will persist
between sessions on that node. NFS is also available in the `/net` directory.

### Reserving GPUs

By default, sessions aren't assigned a GPU. If you need GPUs, use the `--gpus <count>`
flag when creating a session e.g.:

```
beaker session create --gpus 2
```

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

Beaker Interactive supports both [Docker](https://docker.com) and [Beaker](../concept/images)
images. To use a Docker image, prefix the image name with `docker://`. To use a
Beaker image use the `beaker://` prefix instead.

For example, this command uses the AllenNLP base image and runs the `test-install` command:

```
beaker session create --image docker://allennlp/allennlp -- allennlp test-install
```

Some features, like user mapping, will not work with all images. For instance, In the AllenNLP
image, you will see a prompt like `I have no name!@fd82c7800efa:~$`. Please contact the
Beaker team if you need help setting up a custom environment for your interactive sessions.

### Image Caching

By default Beaker Interactive will use a local copy of an image if one exists. For example, if
you've run `docker pull allennlp/allennlp` before, then starting a session using that image won't
pull the latest version to the host:

```
beaker session create --image docker://allennlp/allennlp
```

You can change this behavior using the `--pull` option. The option can be
one of `always`, `missing` or `never`, and defaults to `missing`. If the value is `always`,
the latest version is always retrieved from the source:

```
beaker session create --image docker://allennlp/allennlp --pull always
```

If the value is `missing` the image will only be pulled if it doesn't already exist locally.

Setting the value to `never` means the image won't ever be pulled, which means you'll see
an error if it doesn't exist in the local cache:

```
beaker session create --image docker://php --pull never
Starting session 01F51DJY6PQEWFZRVVDQ7072A2... (Press Ctrl+C to cancel)
Verifying image (docker://php)...
Error: No such image: php
```

### Building Custom Images

At some point you might need to install additional software in your interactive session. You can
do this without exiting the session, but the changes won't persist after the session
stops. To persist those changes you'll need to build a custom image. For example
let's assume we want to create an image with  [jq](https://stedolan.github.io/jq/) installed.

Start by creating a `Dockerfile`:

```
touch Dockerfile
```

If you're unfamiliar with the `Dockerfile` syntax, you can read more about it [here](
https://docs.docker.com/engine/reference/builder/).

Next chose a base image to start from. If you're unsure of what to use it's probably
good to use the [allenai/base](https://hub.docker.com/r/allenai/base/tags) image:

```Dockerfile
FROM allenai/base:cuda11.2-ubuntu20.04
```

Now we'll add a `RUN` entry to the `Dockerfile` that installs `jq`:

```Dockerfile
RUN wget -O /usr/bin/jq https://github.com/stedolan/jq/releases/download/jq-1.6/jq-linux64 && \
	chmod 751 /usr/bin/jq
```

After that's done we can build an image using the `docker build` command. The resulting image
will only exist in our local cache:

```
docker build -t allenai/base:cuda11.2-ubuntu20.04-jq1.6 .
```

Now you can start a session using that image like so:

```
beaker session create --image docker://allenai/base:cuda11.2-ubuntu20.04-jq1.6
```

...and you should now be able to use `jq` like so:

```
[BEAKER] sams ~ $ echo '{ "jq": "is pretty great!" }' | jq
{
  "jq": "is pretty great!"
}
```

At this point the image **only** exists in your local cache. The local image cache is periodically
flushed, so you'll need to push the image to a remote repository if you'd like to use
it again later or share it with other folks.

You can push your image to either [Docker Hub](https://hub.docker.com) or
[Beaker Images](https://beaker.org/images). If you're unsure of which to use, use Beaker. Beaker
images are easier to use, can be used in [Beaker Experiments](../concept/experiments) and
support fine-grained access control.

To push the image to Beaker Images, use the `beaker image create` command. You'll need to execute
this command on the host, not in the interactive session -- which you can exit by either
typing `exit` or pressing `Ctrl^D`.

```
beaker image create allenai/base:cuda11.2-ubuntu20.04-jq1.6 \
    --name "base-cuda-jq" \
    --description "allenai/base with jq too!"
```

Once that's complete, you can start another interactive session using the name you gave the
Beaker image:

```
beaker session create --image beaker://base-cuda-jq
```

That command will work on other hosts too, and the image will be persisted indefinitely so you
can continue to use it.

## Git Authentication

You can authenticate with GitHub using a personal access token or
[an SSH key](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).
Here, we describe authenticating with an access token.

First, generate a new personal access token [here](https://github.com/settings/tokens/new)
and grant it the repo scope.

![Generating a personal access token with repo scope](/docs/images/github-personal-access-token-scope.png)

You will only be able to view the token on GitHub when it is created so store it in 1Password
for later use.

Now, you should be able to clone any GitHub repository over HTTPS.
When prompted for your password, enter the personal access token.

### Cache Credentials

To avoid having to enter you credentials on every Git operation, run the following:

```
git config --global credential.helper store
```

This will cache your personal access token at `~/.git-credentials`.

### Name and Email

Before making a commit, you must tell Git who you are:

```
git config --global user.name <name>
git config --global user.email <email>
```

This will be cached in `~/.gitconfig` and persist across sessions on the same machine.

### Deleting a Token

If your token is compromised, you can delete it [here](https://github.com/settings/tokens).

![Deleting a personal access token](/docs/images/github-personal-access-token-delete.png)
