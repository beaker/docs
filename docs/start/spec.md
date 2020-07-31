# Create an Experiment Spec

In this example, you'll run your experiment using your image from the CLI and also create a corresponding experiment spec.

You'll use your existing image and dataset, as defined in the [prior example](image.md). So, complete that step first if needed.

## Spec Format

Experiment specs are written in YAML or JSON. For brevity, we'll use YAML throughout documentation.  The following example outlines most features of the current experiment spec.

```yaml
tasks:
- name: my-first-task

  # (required) Cluster sets where the task should run.
  # See 'beaker cluster create'
  cluster: ai2/my-cluster

  # Spec describes the specifics of a process to run.
  spec:
    # (optional) The Beaker image name or ID. Either this or dockerImage is required.
    image: myaccount/myimage

    # (optional) The DockerHub image tag. Either this or image is required.
    dockerImage: busybox:latest 

    # (required) Where results will be placed.
    # Beaker automatically uploads files as the job runs.
    resultPath: /path/to/results

    # (optional) Override the default command in the image.
    args: ['python', '-u', '/code/run.py']

    # (optional) Environment variables as a key/value map.
    env:
      A_NUMBER: '0.5'
      A_STRING: foobar
      # ...

    # (optional) Mount datasets as directories into the task.
    datasetMounts:
      - datasetId: training-files
        containerPath: /traindata

      # Mount '/foo.yaml' from a config dataset to '/path/to/config.yml'.
      # The subPath field mounts files or subdirectories within a dataset.
      - datasetId: config-dataset 
        subPath: foo.yaml
        containerPath: /path/to/config.yml

    # (optional) Set minimum resource requirements for larger tasks.
    # All fields are optional, and any number can be set.
    requirements:
      memory: '7.5GiB' # number followed by unit suffix
      cpuCount: 1.5    # number of logical cores
      gpuCount: 2      # must be an integer

- name: my-second-task
  spec:
    # ...

  # (optional) Set dependencies between tasks.
  dependsOn:
      # (required) Name the task to depend on.
    - parentName: my-first-task

      # (optional) If set, mount the result of that task to the following directory.
      containerPath: /path/to/input
```

## From the Command Line

To run an experiment, first write a spec file.  For example, the following runs the classic mnist machine learning task.

```yaml
tasks:
- spec:
    image: mymnist
    resultPath: /output
    env:
      EPOCH: '50'
    datasetMounts:
    - datasetId: mymnist-dataset
      containerPath: /data
```

Experiments can be run with `beaker experiment create`.

```
$ beaker experiment create -f mnist.yaml

Experiment ex_lhqimp6vaffk submitted. See progress at https://beaker.org/ex/ex_lhqimp6vaffk
```
