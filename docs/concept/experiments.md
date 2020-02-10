# Experiments and Tasks

## Experiments

An experiment is the main unit of execution in Beaker.  For example, an experiment could be:

* Running wordcount over Moby Dick.
* Training the BiDAF model within AllenNLP.
* Parsing millions of sentences from the [Gigaword Corpus](https://catalog.ldc.upenn.edu/LDC2003T05).

Experiments have their own page and URL, allowing the results to be shared across the company or the internet.

![Experiment example](../images/beaker-ex.png)

## Tasks

Tasks are Beaker's fundamental unit of work.  A Beaker experiment may contain multiple tasks.  A task may also depend on the results of another task in its experiment, creating an execution graph.

In the following example, the `merge` task depends on the results of the `count-1`, `count-2`, and `count-3` tasks.  The count tasks are executed in parallel and the merge task runs automatically once they complete.

![Parallel wordcount experiment graph](../images/parallel-wordcount.png)
