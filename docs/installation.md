# Installation

:::tip
These are the legacy v4 docs. For other versions, use the links in the header dropdown.
:::

[[toc]]

## Package

zsh-abbr is available on Homebrew. Run

```
brew install olets/tap/zsh-abbr@4
```

and follow the post-install instructions logged to the terminal.

## Plugin

You can install zsh-abbr with a zsh plugin manager. Each has their own way of doing things. See your package manager's documentation or the [zsh plugin manager plugin installation procedures gist](https://gist.github.com/olets/06009589d7887617e061481e22cf5a4a); Fig users can install zsh-abbr from [its page in the Fig plugin directory](https://fig.io/plugins/other/zsh-abbr_olets).

After adding the plugin to the manager, restart zsh:

```shell:no-line-numbers
exec zsh
```

## Manual

Clone this repo and add `source path/to/zsh-abbr.zsh` to your `.zshrc`. Then restart zsh:

```shell:no-line-numbers
exec zsh
```
