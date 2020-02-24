# Count words

First, create an account at [beaker.org](https://beaker.org) and follow the instructions in your [account settings](https://beaker.org/user). See [Install](../first/install.md) for more options.

Reach out to a Beaker admin (#beaker-users or bunsen@allenai.org) to get "Scientist" credentials in order to get authorization to create experiments.

The following example [counts words](https://beaker.org/bp/im_qbjvcda1sed7) in the text of [Moby Dick](https://beaker.org/ds/ds_1hz9k6sgxi0a).

```bash
beaker experiment run \
  --name wordcount-moby \
  --image examples/wordcount \
  --source examples/moby:/input \
  --result-path /output
```
