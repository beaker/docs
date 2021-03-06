# Count Words in Parallel

This example uses two images:
1. `examples/wordcount`: Counts all words in the `/input` directory and produces a `word_count` metric.
2. `examples/merge-wordcount`: Sums the `word_count` metrics in the `/input` directory.

To run an experiment with multiple tasks, you must define an experiment specification in YAML.
The full spec is available [here](../parallel-wordcount.yml).

Run the experiment using `beaker experiment create`:

```
▶ beaker experiment create -f parallel-wordcount.yml
Experiment ex_k6biwrvfcgbu submitted. See progress at http://beaker.allenai.org/ex/ex_k6biwrvfcgbu
```
