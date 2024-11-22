# Uniform Workspace

Uniform Workspace (UW) is a Bash script that provides CLI and API for the most common software development routines.
While various build tools like GNU Make, CMake, Ninja offer flexibility, UW does the opposite, and restricts all
actions to a selected few. It also takes into account that the software is developed for a particular system, which
implies interaction with it.

## Installation

To install the latest stable version, run the following command:

```shell
# This assumes "$HOME/.local/bin/uw" is in PATH. Any other path can be used.
curl -o "$HOME/.local/bin/uw" "https://raw.githubusercontent.com/deohayer/uw/main/uw"
```

After installation, there are the following global commands:

```shell
# Enable completion and aliases (modifies "$HOME/.bashrc").
uw app-setup
# Remove completion and aliases.
uw app-reset
# Update to the latest stable version. It does not require running "uw app-setup" again.
uw app-update
```

There is no "app-uninstall", but clean uninstall can be done this way:
```shell
uw app-reset
rm "$(which uw)"
```

## CLI

There are two types of commands:
 * Global, can be run anywhere.
 * Local, can be run from the workspace only, should be implemented by the user (see Scripts).

### Global commands

#### `app-setup`

Enables `uw` autocompletion and aliases for the local commands. To achieve that, the command modifies `$HOME/.bashrc`
by appending a single line that sources `uw` with special command line parameters.

It should be run only once after installation, and never again afterwards.
It is OK to not run it all, but without autocompletion and aliases `uw` is not that convenient.

#### `app-reset`

Reverts changes done by `app-setup`. Note that it is not perfect and most likely will leave an extra newline.

#### `app-update`

Downloads the latest version of the script - `uw` script from the `main` branch of the Git repository.
`curl` must be installed.

#### `init`

Initializes a workspace in the given directory, if provided (defaults to the current).
It generates a `.uw` subdirectory with all the scripts mentioned in the API subsection Scripts.
The scripts contain the actual actions that mustbe performed by the local commands.
By default, they are not implemented and fail with a non-zero exit code.

### Local commands

Commands are logically divided into two groups:
 * Hardware: `uwa`, `uwd`, `uwp`, `uws`, `uwc`. They are for interaction with hardware and exclude any software-related actions.
 * Software: `uwf`, `uwb`, `uwi`, `uwt`, `uwx`. They are for everything else, and may interact with hardware.

"Hardware" is an abstract term in the context of UW and may refer to anything:
laptop, smartphone, development board, virtual machine, and so on.

#### uwa

Attach to the given hardware.

#### uwd

Detach from the currently attached hardware.

#### uwp

Power management for the currently attached hardware.

#### uws

Execute a shell command or start an interactive shell session in a currently attached hardware

#### uwc

Copy files to or from the currently attached hardware.

#### uwf

Fetch software sources.

#### uwb

Build software images.

#### uwi

Install software images, typically to the currently attached hardware

#### uwt

Test software images, typically on the currently attached hardware.

#### uwx

Extensions. It can be one action, but in most cases it is a collection of useful subcommands.

## API

### Scripts

All files that correspond to the local commands are located under `.uw`, with an extra `env.sh`,
whihc is a common environment for all comands.
Whenever any `uw*` command is executed, it sources the corresponding `uw*.sh` script. The mentioned
`env.sh` is sourced by `uw_init` - that is, **inside** the `uw*.sh`, **not before** it.

### Functions

#### `uw_init`

Takes care of the `--help` option and completion.
Called by default in each `uw*.sh` script, simply do not remove it.

#### `uw_fail`

Prints the provided message to stderr, and exits with the specified exit code (1 by default).
This function is provided purely for convenience.

### Variables

#### `UW_DIR`

An absolute path to the workspace root - a directory that contains ".uw".

#### `UW_SUB`

An absolute path to ".uw" subdirectory within the workspace, `$UW_DIR/.uw`.

#### `UW_VER`

The `uw` script version.

#### `UW_HELP`

A help text for a particular `uw*.sh`.
Must be defined before the `uw_init` call.

#### `UW_TGTS`

Suggested values for the TGT argument for a particular `uw*.sh`.
Must be defined before the `uw_init` call.
