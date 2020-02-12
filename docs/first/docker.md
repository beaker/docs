# Install and configure Docker

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
3. When prompted for the user token, enter your token (available from your **user settings** at the Beaker site.

For example:

```
% beaker configure⏎
Beaker Configuration

Press enter to keep the current value of any setting.
Results will be saved to /Users/username/.beaker

Beaker address [localhost:9027]: https://beaker.org⏎
User token []: <your token>⏎
```

Run `beaker configure test` to check that your configuration is correct.

For example:

```
% beaker configure test⏎
Beaker Configuration Test

Authenticating with user token: <your token>

Authenticated as user: "<your username>" (<your id>)
```


