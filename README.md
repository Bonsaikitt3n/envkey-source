# envkey-source

Integrate [EnvKey](https://www.envkey.com) with any language, either in development or on a server, by making your configuration available OS-wide as environment variables.

## Installation

envkey-source compiles into a simple static binary with no dependencies, which makes installation a simple matter of fetching the right binary for your platform and putting it in your `PATH`. An `install.sh` script is available to simplify this.

**Install via bash:**

```bash
curl -s https://raw.githubusercontent.com/envkey/envkey-source/master/install.sh | bash
```

**Install manually:**

Find the [release](https://github.com/envkey/envkey-source/releases) for your platform and architecture, and stick the appropriate binary somewhere in your `PATH` (or wherever you like really).

## Usage

First, generate an `ENVKEY` in the [EnvKey App](https://github.com/envkey/envkey-app). Then:

```bash
eval $(envkey-source ENVKEY [flags])
```

Or with a `.env` file in the current directory that includes `ENVKEY=...` (in development) / an `ENVKEY` environment variable set (on servers):

```bash
eval $(envkey-source [flags])
```

Now you can access your app's environment variables in this shell, or in any process (in any language) launched from this shell.

### Flags

```text
    --cache              cache encrypted config as a local backup (default is false)
    --cache-dir string   cache directory (default is $HOME/.envkey/cache)
-f, --force              overwrite existing environment variables and/or other entries in .env file
-h, --help               help for envkey-source
-v, --version            prints the version
```

### Errors

If you get an error, envkey-source will echo the error string to stdout instead of setting environment variables. For example:

```bash
$ eval $(envkey-source notvalidenvkey)
error: ENVKEY invalid
```

### Security - Preventing Shell Injection

Whenever you use `eval`, you need to worry about shell injection. We did the worrying for you--envkey-source wraps all EnvKey variables in single quotes and safely escapes any single quotes the variables might contain. This removes any potential for shell injection.

### Overriding Vars

By default, envkey-source will not overwrite existing environment variables or additional variables set in a `.env` file. This can be convenient for customizing environments that otherwise share the same configuration. But if you do want EnvKey vars to take precedence, use the `--force` / `-f` flag. You can read more about this topic in the EnvKey [docs](https://docs.envkey.com/overriding-envkey-variables.html).

### Working Offline

envkey-source can cache your encrypted config so that you can still use it while offline. Just use the `--cache` flag. Your config will still be available (though possibly not up-to-date) the next time you lose your internet connection. If you do have a connection available, envkey-source will always load the latest config.

### Examples

Assume you have `GITHUB_TOKEN` set to `cf4b78a2b8356059f340a7df735d0f63` for the `development` environment in the EnvKey App. You generate a local development `ENVKEY`.

In your project's `.env` file (ignored from source control):

```bash
# .env
ENVKEY=GsL8zC74DWchdpvssa9z-nk7humd7hJmAqNoA
```

Run envkey-source (with caching enabled):

```bash
$ eval $(envkey-source --cache)
```

Now `GITHUB_TOKEN` is available in the shell:

```bash
$ echo $GITHUB_TOKEN
cf4b78a2b8356059f340a7df735d0f63
```

Or in any process you launch from this shell:

```bash
$ python
```

```python
>>> import os
>>> os.environ["GITHUB_TOKEN"]
'cf4b78a2b8356059f340a7df735d0f63'
```

You could do exactly the same on a **server**, except instead of putting your `ENVKEY` in a `.env` file, you'll set it as an environment variable (in whatever way you set environment variables for your host/server management platform).

So you set an environment variable on your server:

```bash
ENVKEY=HSyahYDL2jBpyMnkV6gF-2rBFUNAHcQSJTiLA
```

Then you run envkey-source as part of your server start and restart commands, whatever those may be.

```bash
$ eval $(envkey-source) && server-start
```

```bash
$ eval $(envkey-source) && server-restart
```

If you're using envkey-source on a **CI server**, the process is much the same. Set the `ENVKEY` environment variable in your CI interface, then run `eval $(envkey-source)` before running tests.

### envkey-source within scripts

Note that if you run envkey-source inside a script, your environment variables will only be visible to commands run within that script unless you run the script with `source`, in which case they will be set in the current shell.

## Further Reading

For more on EnvKey in general:

Read the [docs](https://docs.envkey.com).

Read the [integration quickstart](https://docs.envkey.com/integration-quickstart.html).

Read the [security and cryptography overview](https://security.envkey.com).

## Need help? Have questions, feedback, or ideas?

Post an [issue](https://github.com/envkey/envkey-source/issues) or email us: [support@envkey.com](mailto:support@envkey.com).
