# Your First Beaker Experiment

Beaker manages experiments as Docker containers combined with additional metadata to make your code and data easy to share and replicate. To show you how to do this with "real" code and data, this tutorial will show you how to train a Python Pytorch model using LeCun's MNIST dataset of images of handwritten digits.

Don't worry if you don't know about Python, Pytorch, or MNIST data; you won't need to here. This exercise simply shows you how to run a full experiment with Beaker in a way that should be easy to understand.

## Running your experiment from the CLI

For these instructions to work, you must have completed [installing Beaker](install.md). When installed:

1. Go to the existing [MNIST Example experiment's spec page](https://beaker.org/ex/ex_xf0w8hlbdpl6/spec).

   ![Spec for MNIST example](../images/ex_spec.png)

   The spec format is described in more detail [here](../concept/experiments.md#spec-format)

2. Click **Download** to save the experiment's YAML spec file to a location on your local hard drive.

3. From the command line at this directory, enter:

   ```bash
   beaker experiment create MNIST_Example.yaml
   ```

You should see the following output:

```bash
Experiment <experiment_id> submitted. See progress at https://beaker.org/ex/<experiment_id>
```

The resulting experiment ID is unique to every experiment run.

You can see your experiments tasks progress to completion at the URL provided by this output (for example, [https://beaker.org/ex/ex_xf0w8hlbdpl6/tasks](https://beaker.org/ex/ex_xf0w8hlbdpl6/tasks)).

That's how an experiment is defined and shared by its spec, the YAML file containing the necessary metadata with which you can manage an experiment.

If both of the above steps are working for you, you are ready to proceed to creating your own Beaker image and dataset, which is the [next example](image.md).
