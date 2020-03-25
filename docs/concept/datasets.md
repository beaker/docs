# Datasets

Datasets are immutable collections of one or more files.  Datasets can be mounted into experiments
for data re-use and to clearly show what information was needed to run an experiment.
The following example shows how to create and use datasets.

## Preparation: Create Some Files

First create a working directory to contain our example.

```bash
mkdir -p ~/dataset-example
cd ~/dataset-example
```

Next we'll create some example files to upload as datasets.

```bash
mkdir -p multifile/nested
echo 'First file' > multifile/first
echo 'Second file' > multifile/second
echo 'Nested file' > multifile/nested/file
echo 'This is a single file.' > singlefile
```

Verify the file contents. The following command finds all files in the current
directory and prints each file's path and contents.

```
▶ find . -type f -exec echo {} \; -exec cat {} \; -exec echo \;
./multifile/first
First file

./multifile/second
Second file

./multifile/nested/file
Nested file

./singlefile
This is a single file.
```

## Upload

You can upload any file or collection of files as a dataset with a single command. Here we'll create
two datasets.

Our first dataset is a single file. Single-file datasets are treated by the system as a file.

```
▶ beaker dataset create --name my-file-dataset ./singlefile
Uploading my-file-dataset (ds_as6t74lspoc5)...
Done.
```

Our second dataset contains several files. This type of dataset is treated by the system as a
directory. _(To upload a single file as a directory, simply place the file in an empty directory and
upload the directory.)_

```
▶ beaker dataset create --name my-dir-dataset ./multifile
Uploading my-dir-dataset (ds_4kjgriri1sro)...
Done.
```

Notice that each dataset is assigned a unique ID in addition to the name we chose. Any object,
including datasets, can be referred to by its name or ID. Like any object, a dataset can be renamed,
but its ID is guaranteed to remain stable. The following two commands are equivalent:

```bash
beaker dataset inspect my-file-dataset
beaker dataset inspect ds_as6t74lspoc5
```

## Inspect

A dataset can be inspected with `beaker dataset inspect`, which produces a JSON representation of
the dataset.

```
▶ beaker dataset inspect my-file-dataset
[
    {
        "id": "ds_as6t74lspoc5",
        "name": "my-file-dataset",
        "owner": {
          ...
        },
        "author": {
          ...
        },
        "workspaceRef": {
          ...
        },
        "created": "2020-02-13T23:47:10.309419Z",
        "committed": "2020-02-13T23:47:11.413472Z",
        "archived": false,
        "storage": {
            "address": "https://data.beaker.org",
            "id": "01e10f5y3qfhe2wtkatcm7c2sp",
            "token": "...",
            "tokenExpires": "2020-02-14T11:48:35.778244357Z"
        }
    }
]
```

## Download

You can download a dataset to your local drive at any time with `beaker dataset fetch`. Beaker's
`fetch` command follows the same rules as the standard `cp` command. The following example downloads
the single-file dataset to an empty directory. Notice how the original filename is restored by default.

```
▶ mkdir fetched
▶ beaker dataset fetch -o fetched my-file-dataset
Downloading dataset ds_as6t74lspoc5 to file fetched/singlefile ... done.

▶ cat fetched/singlefile
This is a single file.
```

## Use Datasets in an Experiment

To demonstrate how to use a dataset in an experiment, we'll run the same find command we ran above
as a Beaker experiment. The code for this experiment's image can be found
[here](../examples/list-files).

```bash
cat > find.yaml << EOF
tasks:
- name: list-files
  spec:
    image: examples/list-files
    resultPath: /results
    env:
      LIST_DIR: /data
    datasetMounts:
    - datasetId: my-file-dataset
      containerPath: /data/single
    - datasetId: my-dir-dataset
      containerPath: /data/single
EOF
```
```
beaker experiment create -f find.yaml
```

This command will print a URL to your experiment, which should complete momentarily. Observe the
logs emitted from the experiment should be similar to the find command above.

## Cleanup

To clean up, simply remove the `~/dataset-example` directory we created at the beginning.

To maintain reproducible experiments, you should be cautious about deleting datasets.  That said,
large datasets can cost a significant amount indefinately.  You can delete a dataset with
`beaker dataset delete`.
