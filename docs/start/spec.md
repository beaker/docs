# Create an Experiment Spec

In this example, you'll run your experiment using your blueprint from the CLI and also create a corresponding experiment spec.

You'll use your existing blueprint and dataset, as defined in the [prior example](image.md). So, complete that step first if needed. 

# From the Command Line 

Enter the following from your Terminal shell, using the This example assumes you've successfully completed [Beaker and Docker installation](install.md), and you've set up your [Beaker.org](https://www.beaker.org) account so that you can run experiments as shown in [Your First Experiment](experiment.md).

https://beaker.org/ex/ex_lhqimp6vaffk

```
$ beaker experiment create \
    --blueprint mymnist \
    --env EPOCH=50 \
    --source mymnist-dataset:/data \
    --result-path /output
    
Experiment ex_lhqimp6vaffk submitted. See progress at https://beaker.org/ex/ex_lhqimp6vaffk
```
