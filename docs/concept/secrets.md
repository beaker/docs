
# Secrets

Secrets are used to pass sensitive information like API keys and passwords to tasks.

Each secret is tied to a workspace.

## Creating a Secret

Secrets can be created from the [Beaker CLI](https://github.com/allenai/beaker).

Creating a secret from the command line:

```bash
beaker secret write --workspace=ai2/{workspace} secret-name value
```

Creating a secret from stdin:

```bash
echo "value" | beaker secret write --workspace=ai2/{workspace} secret-name
```

Creating a secret from a file:

```bash
cat file | beaker secret write --workspace=ai2/{workspace} secret-name
```

Creating a secret from an environment variable:

```bash
beaker secret write --workspace=ai2/{workspace} secret-name $VALUE
```

## Listing Secrets

```
beaker secret list --workspace=ai2/workspace
```

```
NAME        CREATED              UPDATED
secret-name 2020-11-17T00:33:52Z 2020-11-17T00:33:52Z
```

## Reading a Secret

```
beaker secret read --workspace={workspace} {secret name}
```

## Using a Secret in a Task

Secrets can be mounted as files or used as environment variables.

### Files

This example will mount the secret `SECRET` as a file at `/secret-file`.

```yaml
  datasets:
    - mountPath: /secret-file
      source:
        secret: SECRET
```

### Environment Variables

This example will make the secret `SECRET` available as the environment variable `SECRET_ENV_VAR`.

```yaml
  envVars:
    - name: SECRET_ENV_VAR
      secret: SECRET
```
