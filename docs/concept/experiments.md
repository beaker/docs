# Experiments and Tasks

## Experiments

An experiment is the main unit of execution in Beaker.  For example, an experiment could be:

* Running wordcount over Moby Dick.
* Training the BiDAF model within AllenNLP.
* Parsing millions of sentences from the [Gigaword Corpus](https://catalog.ldc.upenn.edu/LDC2003T05).

Experiments have their own page and URL, allowing the results to be shared across the company or the internet.

![Experiment example](../images/beaker_ex.png)

## Tasks

Tasks are Beaker's fundamental unit of work.  A Beaker experiment may contain multiple tasks.  A task may also depend on the results of another task in its experiment, creating an execution graph.

In the following example, the `merge` task depends on the results of the `count-1`, `count-2`, and `count-3` tasks.  The count tasks are executed in parallel and the merge task runs automatically once they complete.

![Parallel wordcount experiment graph](../images/parallel-wordcount.png)

## Spec Format

Below can be found a detailed description of Beaker's experiment specification format. Specs must be
written in YAML or JSON, as in the following example.

```yaml
version: v2-alpha
description: A simple 'hello world' example.
tasks:
  - name: echo
    image:
      docker: busybox:latest
    command: ['/bin/sh', '-c']
    arguments: ['echo "Hello world!"']
    result: # Required even if the task produces no files.
      path: '/unused'
```

### Experiment

Experiment is the root level structure, describing a single task or an ordered
pipeline of tasks to run.

| Field | Type | Required? | Description |
| ----- | ---- | :-------: | ----------- |
| version | string | Yes | Must be `v2-alpha` |
| description | string | No | Long-form explanation for an experiment |
| tasks | [Task](#task) \[\] | Yes | Specifications for each process to run |
| context | [Context](#context) | No | Context defines a default execution environment for all tasks. Required fields may be omitted if each task has defined its own context. |

### Task

Task describes a single job, or process.

| Field | Type | Required? | Description |
| ----- | ---- | :-------: | ----------- |
| name | string | No\* | Name is used for display and to refer to the task throughout the spec. It must be unique among all tasks within its experiment. |
| image | [ImageSource](#imagesource) | Yes | A base image to run, usually built with Docker |
| command | string \[\] | No | Command is the full shell command to run as a list of separate arguments. If omitted, the image's default command is used, for example Docker's `ENTRYPOINT` directive. If set, default commands such as Docker's `ENTRYPOINT` and `CMD` directives are ignored.<br><br>Example: `["python", "-u", "main.py"]` |
| arguments | string \[\] | No | Arguments are appended to the `command` and replace default arguments such as Docker's `CMD` directive. If `command` is omitted, arguments are appended to the default command, Docker's `ENTRYPOINT` directive.<br><br> Example: If Command is `["python", "-u", "main.py"]`, specifying arguments `["--quiet", "some-arg"]` will run the command `python -u main.py --quiet some-arg`. |
| envVars | map\[string, string\] | No | Environment variables set before the task is run |
| datasets | [DataMount](#datamount) \[\] | No | External data sources mounted into the task as files |
| result | [ResultSpec](#resultspec) | Yes | Where the task will place output files |
| context | [Context](#context) | Yes | Context describes how and where this task should run. Some fields are required and must be set in either the experiment or the task. If a field is set in both, the task's takes precedence. |
| resources | [TaskResources](#taskresources) | No | External hardware requirements, such as memory or GPU devices |

\* Though tasks may be anonymous for compatibility reasons, we recommend always
assigning a name. 

### ImageSource

ImageSource describes where Beaker can find a task's image. Beaker will automatically pull, or
download, this image immediately before running the task.

| Field | Type | Required? | Description |
| ----- | ---- | :-------: | ----------- |
| beaker | string | No\* | Name or ID of a [Beaker image](./images.md) | 
| docker | string | No\* | Tag of a Docker image hosted on the [Docker Hub](https://hub.docker.com) or a private registry. If the tag is from a private registry, the cluster on which the task will run must be pre-configured to enable access. |

\* Exactly one field must be defined.

### DataMount

DataMount describes how to mount a dataset into a task. 

| Field | Type | Required? | Description |
| ----- | ---- | :-------: | ----------- |
| mountPath | string | Yes | The mount path is where Beaker will place the dataset within the task container. Mount paths must be absolute and may not overlap with other mounts.<br><br>Because some environments use case-insensitive file systems, mount paths differing only in capitalization are disallowed. |
| subPath | string | No | Sub-path to a file or directory within the mounted dataset. Sub-paths may be used to mount only a portion of a dataset; files outside of the mounted path are not downloaded.<br><br>Example: Given a dataset containing a file `/path/to/file.csv`, setting the sub-path to `path/to` will result in the task seeing `{mountPath}/file.csv`. |
| source | [DataSource](#datasource) | Yes | Location from which Beaker will download the dataset |

### DataSource

| Field | Type | Required? | Description |
| ----- | ---- | :-------: | ----------- |
| beaker | string | No\* | Name or ID of a [Beaker dataset](./datasets.md) |
| result | string | No\* | Name of a previous task whose result will be mounted. A result source implies a dependency, meaning this task will not run until its parent completes successfully. |

\* Exactly one field must be defined.

### ResultSpec

ResultSpec describes how to capture a task's results. Results are captured as datasets from the
given location. Beaker monitors this location for changes and periodically uploads files as they
change in near-real-time.

| Field | Type | Required? | Description |
| ----- | ---- | :-------: | ----------- |
| path | string | Yes | Directory to which the task will write output files. |

### Context

A Context describes an execution environment, or how a task should be run. Because contexts depend
on external configuration, a given context may be invalid or unavailable if a task is re-run at a
future date.

| Field | Type | Required? | Description |
| ----- | ---- | :-------: | ----------- |
| cluster | string | Yes | Name or ID of a Beaker cluster on which the task should run. |
| priority | string | No | Set priority to change the urgency with which a task will run.<br><br>Values may be `low`, `normal`, or `high`. Tasks with higher priority are placed ahead of tasks with lower priority in the queue.<br><br>Priority may also be elevated to `urgent` through UI. |

## TaskResources

TaskResources describe minimum external hardware requirements which must be available for a task to
run. Beaker guarantees a task will not run until all defined requirements are met. A task may be
given greater resources than defined here.

| Field | Type | Required? | Description |
| ----- | ---- | :-------: | ----------- |
| cpuCount | double | No | Minimum number of logical CPU cores. It may be fractional.<br><br>Examples: `4`, `0.5`. |
| gpuCount | int | No | Minimum number of GPU-cores. It must be non-negative. |
| memory | string | No | Minimum available system memory as a number with unit suffix. `memory` must match `memoryBytes` exactly if both are set.<br><br>Examples: `2.5GiB`, `10240m`. |
| memoryBytes | int | No | Minimum available system memory as an exact byte count. `memoryBytes` must match `memory` exactly if both are set. |
