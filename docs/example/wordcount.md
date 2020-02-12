# Count words 

First, create an account at [beaker.org](https://beaker.org) and follow the instructions in your [account settings](https://beaker.org/user). See [Install](../start/install.md) for more options.
   
Request "Scientist" or higher credentials from a Beaker admin to get authorization to create experiments.

The following example [counts words](https://beaker.org/bp/im_qbjvcda1sed7) in the text of [Moby Dick](https://beaker.org/ds/ds_1hz9k6sgxi0a).

```bash
beaker experiment run \
  --name wordcount-moby \
  --blueprint examples/wordcount \
  --source examples/moby:/input \
  --result-path /output
```
