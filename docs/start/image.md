# Create an Image and Dataset

In this step, you'll use existing experiment code, data, and a Docker file. You'll define your own Beaker *image*, to define and manage the experiment you will run, and a Beaker *dataset* to hold the source data. This example locally reproduces the existing MNIST experiment of the [prior example](experiment.md).

Don't worry if you don't know much about Python, Pytorch, or MNIST data; you don't need to. Rather, this exercise simply shows you how to run a full experiment with Beaker. You should then be able to apply these concepts to your own code, data, and experiments, to manage them with Beaker.

This example assumes you've successfully completed [Beaker and Docker installation](install.md), and you've set up your [Beaker.org](https://www.beaker.org) account so that you can run experiments as shown in [Your First Experiment](experiment.md).

## Set Up Python and Pytorch

If not already set up, install [Python 3](https://www.python.org/downloads/) and [Pytorch (and Torchvision)](https://pytorch.org/get-started/locally/).

After installing, you can verify your configuration by entering `python` from your Terminal shell:

```
$ python
Python 3.7.2 (default, Dec 29 2018, 00:00:04)
[Clang 4.0.1 (tags/RELEASE_401/final)] :: Anaconda, Inc. on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
```
This shows your Python version, date, and so on, if successfully configured.

From the Python prompt (`>>>`, above) you can verify your Pytorch installation too:

```python
>>> import torch; print(torch.__version__)
1.0.0
```

Which shows your Pytorch/Torchvision version, if successfully configured.

Finally, if you are new to Python for this tutorial, know you quit the Python shell (`>>>`) and return to your Terminal shell by pressing `control+d`. Make sure these configurations are set up before proceeding.

## Get the existing code and data

For this tutorial, use the code from [https://github.com/beaker/mnist-example](https://github.com/beaker/mnist-example).

1. Download https://github.com/beaker/mnist-example to your machine.

2. Download the dataset from from [here](https://beaker.org/ds/ds_kf6v919aq7hk/details) to a subdirectory named `data` in `mnist-example-master`.  The *dataset* contains the files referenced by the code of the experiment. A convenient way to download the MNIST dataset from Beaker is to go to your `mnist-example-master` directory from your Terminal shell, then run:

  ```
  $ beaker dataset fetch --output=./data ds_kf6v919aq7hk
  ```

You can get the dataset ID (ds_kf6v919aq7hk) from the dataset page on [Beaker.org](https://beaker.org/ds/ds_kf6v919aq7hk/details).

The result is output:

```
Downloading dataset ds_kf6v919aq7hk to directory ./data/ ... done.
```

Also, your `mnist-example-master/data` subdirectory should contain:

```
train-images-idx3-ubyte
t10k-images-idx3-ubyte
train-labels-idx1-ubyte
t10k-labels-idx1-ubyte
```

## Run the code locally

Some environment variables must be exported for the Python code to run.

1. Export the python path:

   ```
   $ export PYTHONPATH=.
   ```

2. Set and export the EPOCH environment variable, required by this experiment:

   ```
   $ export EPOCH=10
   ```

3. From your `mnist-example-master` directory, run the main program with Python 3:

   ```
   $ python3 beaker_pytorch/main.py
   ```

You should see the code run, then conclude with a message such as:

```
...
Test set: Average loss: 0.2057, Accuracy: 9405/10000 (94%)
```

By default, this code puts results in an `/output` subdirectory, in `metrics.json`.

If this doesn't run for you, double-check your Python (or perhaps Beaker) configurations; note that Python 3.6.5 or later is required by this experiment.

All of the above simply represents an experiment's code and dataset running locally, as you would do without using Beaker. This code produces locally what the [prior example](experiment.md) produced using the Beaker cloud.

The next step towards packaging this code and data so it can be run in Beaker is to build a corresponding a Docker image.

## Build a Docker image

To build the docker image from the existing Dockerfile (which you cloned from Github), from the command line run:

```
$ docker build -t mymnist .
```

You should see the Docker steps complete, and conclude successfully with a message such as:

```
...
Successfully tagged mymnist:latest
```

This Docker CLI command instructed Docker to build an image based on the files at the current directory, using the Dockerfile that you cloned to this location, and you tagged it `mynist`.

In later examples, we'll show you how to set up your own Dockerfile for building images. For now, simply use the provided Dockerfile that you downloaded from Github. If you want, you can view the Dockerfile's txt contents to see its base image, ENV instructions, and so on. For now, don't make any changes to it.

## Create a Beaker image

Now you have a Docker image of a complete local experiment's codebase and dataset. Next, create a Beaker *image* to represent this experiment and push it to Beaker.org for management and reuse.

```
$ beaker image create -n <mymnist> mnist
```

Note can have only one Beaker image called mymnist. So, if you've created a mymnist blueprint previously, change <mymnist> to a unique name, such as mymnist2.

If you successfully create the image, you should see output such as:
```
Pushing mymnist as mnist (im_8ugouwgec4gn)...
<...preparing, waiting, etc...>
The push refers to repository [gcr.io/ai2-beaker-core/public/bhq49ga41h4qcklhc79g]
latest: digest: sha256:569de2a77ba779dbfddf6cc897f7abb17ef674239021b5f05e63e596aa7db5c3 size: 3058
Done.
$
```

Notice that each image is assigned a unique ID, in addition to the name we chose. Any object,
including images, can be referred to by either its name or ID. Like any object, a blueprint can be
renamed, but its ID is guaranteed to remain stable. The following two commands are equivalent:

### Inspect the image

```
$ beaker image inspect mymnist
$ beaker image inspect im_8ugouwgec4gn
```

Either should produce CLI output such as:

```
[
    {
        "id": "im_8ugouwgec4gn",
        "user": {
            "id": "<your_user_id>",
            "name": "<your_user_name>",
            "display_name": ""
        },
        "name": "mymnist",
        "created": "2019-02-25T19:51:00.116911Z",
        "committed": "2019-02-25T19:51:05.103003Z",
        "original_tag": "mnist"
    }
]
```

### Pull an image

You can pull your image to your local machine at any time with `beaker image pull`.

```
$ beaker image pull mymnist
Pulling gcr.io/ai2-beaker-core/public/bduufrl06q5ner2l0440 ...
latest: Pulling from ai2-beaker-core/public/bduufrl06q5ner2l0440
Digest: sha256:4c70545c15cca8d30b3adfd004a708fcdec910f162fa825861fe138200f80e19
Status: Downloaded newer image for gcr.io/ai2-beaker-core/public/bduufrl06q5ner2l0440:latest
Done.
```

In order to avoid accidentally overwriting your local images, this command assigns Beaker's random
internally assigned tag  `gcr.io/ai2-beaker-core/public/bduufrl06q5ner2l0440` by default. To assign
a more human-friendly tag, set it with an additional argument:

```
$ beaker image pull mymnist friendly-name
```

## Create a dataset

You can upload any file or collection of files as a dataset with the `beaker dataset` command.

First, recall from above, this directory contains the four image source files used by this code:

```
$ ls data
t10k-images-idx3-ubyte	t10k-labels-idx1-ubyte	train-images-idx3-ubyte	train-labels-idx1-ubyte
```

As our source data contains several files, this type of dataset is treated by Beaker as a
directory. From your CLI enter:

```
$ beaker dataset create --name mymnist-dataset ./data
```

```
Uploading mymnist-dataset (ds_q76gp0s33d01)...
Done.
```
Notice that the dataset is assigned a unique ID (above, `ds_q76gp0s33d01`; yours will differ) in addition to the name we chose (`mymnist-dataset`). Like images, datasets can be referred to by its name or ID. A dataset can be renamed, but its ID is guaranteed to remain stable. The following two commands are equivalent:

```
$ beaker dataset inspect mymnist-dataset
$ beaker dataset inspect ds_q76gp0s33d01
```

### Inspect the dataset

A dataset can be inspected with `beaker dataset inspect`, which produces a JSON representation of
the dataset. An optional `--manifest` flag, if provided, will also produce the dataset's contents.

```
$ beaker dataset inspect --manifest mymnist-dataset
[
    {
        "id": "ds_q76gp0s33d01",
        "user": {
            "id": "<your_user_id>",
            "name": "<your_user_name>",
            "display_name": ""
        },
        "name": "mymnist-dataset",
        "created": "2019-02-25T22:50:57.793211Z",
        "committed": "2019-02-25T22:51:21.968051Z",
        "manifest": {
            "id": "ds_q76gp0s33d01",
            "files": [
                {
                    "file": "/.DS_Store",
                    "size": 6148,
                    "time_last_modified": "2019-02-25T22:50:57.98Z"
                },
                {
                    "file": "/t10k-images-idx3-ubyte",
                    "size": 7840016,
                    "time_last_modified": "2019-02-25T22:51:02.019Z"
                },
                {
                    "file": "/t10k-labels-idx1-ubyte",
                    "size": 10008,
                    "time_last_modified": "2019-02-25T22:51:02.418Z"
                },
                {
                    "file": "/train-images-idx3-ubyte",
                    "size": 47040016,
                    "time_last_modified": "2019-02-25T22:51:21.097Z"
                },
                {
                    "file": "/train-labels-idx1-ubyte",
                    "size": 60008,
                    "time_last_modified": "2019-02-25T22:51:21.644Z"
                }
            ]
        }
    }
]
```

### Download

As you might recall from setting up this code and data earlier, you can download a dataset to your local drive at any time with `beaker dataset fetch`. Beaker's
`fetch` command follows the same rules as the standard `cp` command. The following example downloads
the mymnist-dataset dataset to an empty directory. Notice how the original filename is restored by default.

```
$ mkdir fetched
$ beaker dataset fetch -o fetched mymnist-dataset
Downloading dataset ds_q76gp0s33d01 to directory fetched/ ... done.
```

You already have this data, since you just created this dataset by uploading it. But, you could now clean up your local system by removing your local files, then restore your dataset from Beaker in the future with `beaker dataset fetch`. Or, easily share with others or migrate an experiment to a new machine.

If all of the above is working for you, you should proceed to create your own experiment spec, which is the [next example](spec.md).
