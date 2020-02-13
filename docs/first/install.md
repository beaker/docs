# Set up Beaker

## Join

First, sign up for your [Beaker](https://beaker.org) account.

1. From Beaker.org, click **Log In**.
2. From Beaker Public, click **Sign Up**.
3. Choose **SIGN UP WITH GOOGLE** and proceed to create and confirm your Beaker account.

If you already have an account, make sure to sign in before using Beaker.

1. From Beaker.org, click **Log In**.
2. From Beaker Public, click **LOG IN WITH GOOGLE**.
3. Choose the Google account with which you signed up for Beaker.

When signed in, you'll see your user name in the top-right corner of [Beaker.org](https://beaker.org) and have access to your **Experiments**, **Datasets** and **Groups** pages.

## Install

There are a few different ways to install Beaker:

- Download a
[release](https://github.com/allenai/beaker/releases) and extract it:

    ```bash
    tar -xvzf beaker_*.tar.gz -C /usr/local/bin
    ```
    
    Place the binary executable in your PATH (e.g., /usr/local/bin/ or ~/bin).

- macOS users can install Beaker through [Homebrew](https://brew.sh/) with a custom tap:

    ```bash
    brew tap allenai/homebrew-beaker https://github.com/allenai/homebrew-beaker.git
    brew install beaker
    ```

- [GoLang](https://golang.org/) developers can install Beaker from source using standard tools:

    ```bash
    go get -u github.com/allenai/beaker/...
    ```
  
## Configure and Test

To set up Beaker:

1. Run `beaker configure`.
2. When prompted for the Beaker address, enter https://beaker.org
3. When prompted for the user token, enter your token. Your token is available from your **Settings** at [Beaker.org](https://beaker.org). To find it, when signed in to Beaker, from the top-right corner click your **user name**, **Settings**.

Run `beaker configure test` to check that your configuration is correct.

For example:

```
% beaker configure test⏎
Beaker Configuration Test

Authenticating with user token: <your token>

Authenticated as user: "<your username>" (<your id>)
```

## Docker

You must install Docker to use Beaker, because Beaker packages experiment code using Docker containers.

Install the appropriate Docker Desktop version from the [Docker site](https://www.docker.com/products/docker-desktop), following Docker's instructions.

## Next step

When you have your Beaker.org account, Beaker, and Docker each installed and configured, proceed to [your first experiment](first.md) to learn the fundamentals of experiments with Beaker. 
