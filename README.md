# Zsh bitbake completion

![example workflow](https://github.com/antznin/zsh-bitbake/actions/workflows/main.yml/badge.svg)

This plugins adds completion for the
[`bitbake`](https://git.openembedded.org/bitbake) and `devtool` command line
tool in the Z shell.

## Installation

* [`oh-my-zsh`](https://ohmyz.sh):

  * Clone this repository in `$HOME/.oh-my-zsh/custom/plugins/`:

    ```shell
    git clone https://github.com/antznin/zsh-bitbake.git "$HOME"/.oh-my-zsh/custom/plugins/zsh-bitbake
    ```

  * Add `zsh-bitbake` to the plugins array in your zshrc file:

    ```zsh
    plugins=(... zsh-bitbake)
    ```

* To install the plugin separately, clone this repository and source
  `bitbake.plugin.zsh` in your zshrc. Then add this repository's path to the
  completion path:

  ```zsh
  source ".../zsh-bitbake/bitbake.plugin.zsh"
  fpath+=".../zsh-bitbake"
  autoload -U compinit
  compinit
  ```

## Configuration

* `ZSH_BITBAKE_DISABLE_RECIPE_COMPLETION`: an initial command is run to create a
  text file listing all available recipes in the current bitbake environment.
  The file is located in `$BUILDDIR/.recipe-cache`.

  This can take a while depending on the state the build environment. To disable
  this feature, set `ZSH_BITBAKE_DISABLE_RECIPE_COMPLETION` to any value in your
  zshrc.

## How it works

Completion of arguments works normally as any other zsh completion, with
additional suggestions when possible (common bitbake tasks, debug domains…).

Completion of recipes works differently as it depends on the environment. When
trying to complete a recipe for the first time in the current environment, the
`bitbake-layers show-recipes` command is run to fetch the complete list of
recipes.

This command execution has a few requirements (not run and quietly exited if
unmet):

* `ZSH_BITBAKE_DISABLE_RECIPE_COMPLETION` is not set (see
  [Configuration](#configuration)).

* `BUILDDIR` is a directory. This variable is set by the `oe-init-build-env`
  script.

* Commands `bitbake-layers`, `sed`, and `tail` exist in the current shell.

This creates a file `$BUILDDIR/.recipe-cache` that stores the list of recipes.
Recipes are read from this file to enable completion. This file is not
automatically updated if recipes are added or removed in the current environment
(it would be too slow and costly). File must be removed to be regenerated on the
next completion.
