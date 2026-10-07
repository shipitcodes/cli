# Shipcode CLI — public releases

This repository distributes compiled releases of the [Shipcode](https://app.shipit.codes) CLI. The `shipcode` command lets you initialize projects, read stage instructions, run tests, and submit solutions from your terminal.

Download the CLI binaries and installer from **GitHub Releases** using the links below.

- [Download the latest stable release](https://github.com/shipitcodes/cli/releases/latest).
- [Browse all releases and their assets](https://github.com/shipitcodes/cli/releases).

## Supported platforms

| Operating system | Architecture | Download asset |
| --- | --- | --- |
| Linux | x64 | `shipcode-x86_64-unknown-linux-gnu.tar.gz` |
| Linux | ARM64 | `shipcode-aarch64-unknown-linux-gnu.tar.gz` |
| macOS | Intel | `shipcode-x86_64-apple-darwin.tar.gz` |
| macOS | Apple Silicon | `shipcode-aarch64-apple-darwin.tar.gz` |

On Windows, install and run the CLI from a Linux terminal in WSL. A native Windows binary is not currently available.

## Installation

Run this command on Linux or macOS:

```sh
curl -fsSL https://app.shipit.codes/install.sh | sh
```

The installer detects your operating system and architecture, downloads the latest stable release, verifies the archive with SHA-256, and installs `shipcode` in `~/.local/bin`. It requires `curl`, `tar`, `install`, and either `sha256sum` or `shasum`.

If the directory is not already in your `PATH`, add this line to your shell configuration file and open a new terminal:

```sh
export PATH="$HOME/.local/bin:$PATH"
```

Verify the installation:

```sh
shipcode --version
shipcode --help
```

To inspect the installer before running it:

```sh
curl -fLO https://app.shipit.codes/install.sh
less install.sh
sh install.sh
```

### Choose a version or installation directory

Use `SHIPCODE_VERSION` to install a published version and `SHIPCODE_INSTALL_DIR` to change the destination. Replace `0.1.0` with the version you want to download:

```sh
curl -fsSL https://app.shipit.codes/install.sh |
  SHIPCODE_VERSION="0.1.0" SHIPCODE_INSTALL_DIR="$HOME/bin" sh
```

Accepted version formats are `0.1.0`, `v0.1.0`, and `cli-v0.1.0`. To install a prerelease, specify its full version, such as `0.2.0-beta.1`, once it has been published. If you change the installation directory, add it to your `PATH` as well.

### Manual download and verification

From the [releases page](https://github.com/shipitcodes/cli/releases), download the `.tar.gz` archive for your platform and its `.sha256` file. Each archive contains the `shipcode` executable.

For example, on Linux x64:

```sh
(
  set -eu
  base_url="https://github.com/shipitcodes/cli/releases/latest/download"
  asset="shipcode-x86_64-unknown-linux-gnu.tar.gz"
  curl -fLO "$base_url/$asset"
  curl -fLO "$base_url/$asset.sha256"
  sha256sum -c "$asset.sha256"
  tar -xzf "$asset"
  mkdir -p "$HOME/.local/bin"
  install -m 755 shipcode "$HOME/.local/bin/shipcode"
)
```

For another platform, change `asset` to the corresponding filename in the table. On macOS, replace the checksum command with `shasum -a 256 -c "$asset.sha256"`. Verification must succeed before you extract and install the binary.

## Updates

To update to the latest stable release:

```sh
shipcode update
```

The command downloads the archive for your platform, verifies its SHA-256 checksum, and replaces the installed executable. It does not require signing in to Shipcode.

You can also install a specific version:

```sh
SHIPCODE_VERSION="0.1.0" shipcode update
```

Running the installer again also updates the binary in your chosen installation directory.

## Getting started

Git is required to initialize projects and submit solutions. Choose a project on [Shipcode](https://app.shipit.codes), sign in from your terminal, and run the initialization command provided by the platform:

```sh
shipcode auth login
shipcode init sc_init_<your-code>
```

Replace `sc_init_<your-code>` with your actual initialization code. From the generated project directory:

```sh
shipcode task
shipcode test
shipcode submit
shipcode status
```

`task` displays the instructions, `test` runs tests in an isolated cloud environment, `submit` records the result to advance your progress, and `status` displays your progress. Tests and submissions require the Pro plan. Run `shipcode --help` or `shipcode <command> --help` to see the available options.

See the [complete CLI user guide](docs/cli.md) for detailed command usage, options, mentor conversations, local preferences, and troubleshooting.

## Release contents

Releases use tags in the format `cli-vX.Y.Z` and include:

- A `.tar.gz` archive containing the binary for each supported platform.
- A `.sha256` file for each archive to verify its integrity.
- The `install.sh` installer and its `install.sh.sha256` checksum.
- A `SHA256SUMS` file containing the checksums of all distributed assets.

Tags with suffixes such as `cli-v0.2.0-beta.1` are published as prereleases. The `releases/latest` links point to the latest stable release. Public downloads do not require a GitHub account.
