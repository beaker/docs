# Parameter Injection

This example demonstrates how to inject values into an experiment.

## Templates

Values are injected into an experiment specification via Go templates. When
parsing a templated spec file, anything between double curly braces ( `'{{'` and
`'}}'` ) will be evaluated as expressions and replaced.

Exported environment variables can be expanded with built-in `{{.Env.varName}}`.

### Example

The following command demonstrates expansion of an environment variable.

1. Upload the `busybox` Docker image:
   ```bash
   docker pull busybox
   beaker image create --name busybox busybox
   ```

1. Create an experiment which prints a substituted value.
   ```yaml
   version: v2-alpha
   description: Print {{.Env.USER}}
   tasks:
   - name: print
     image:
       beaker: busybox
     arguments: ['sh', '-c', 'echo Parameter value: $ENV']
     envVars:
       ENV: {{.Env.USER}}
     result:
       path: /none
   ```

1. Run: `beaker experiment create -f spec.yaml`

### Additional Reading

See [Go templates](https://golang.org/pkg/text/template/) for an in-depth
description of what is possible with templates.
