# zsh-abbr ![Legacy release 4.x](https://img.shields.io/github/v/release/olets/zsh-abbr?filter=v4.*&label=legacy%20release&color=A20707) 

:::tip
These are the v4 docs. For v5 docs see <https://zsh-abbr.olets.dev>
:::

**zsh-abbr** is the zsh manager for auto-expanding abbreviations - text that when written in a terminal is replaced with other (typically longer) text. Inspired by fish shell.

For example, a frequently-run command like `git checkout` can be abbreviated to `gco` (or even `co` or `c` or anything else). Type <kbd>Space</kbd> after an abbreviation to expand it. Type <kbd>Enter</kbd> after an abbreviation to expand it and run the expansion. To prevent expansion, add <kbd>Ctrl</kbd> (<kbd>Ctrl</kbd><kbd>Space</kbd> / <kbd>Ctrl</kbd><kbd>Enter</kbd>) or add a delimiter like `;` after the abbreviation.

Like zsh's `alias`, zsh-abbr supports "regular" (i.e. command-position) and "global" (anywhere on the line) abbreviations. Like fish's abbr, zsh-abbr supports interactive creation of persistent abbreviations that are immediately available in all terminal sessions.

Run `abbr help` for documentation; if the package is installed with Homebrew, `man abbr` is also available.

## Essential commands

```shell:no-line-numbers
# Add and use an abbreviation
% abbr gc="git checkout"
% gc[Space] # space expands this to `git checkout `
% abbr gcm="git checkout main"
% gcm[Enter] # enter expands this to `git checkout main` and then accepts
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
%

# Abbreviations are immediately available to all current and future sessions
% source ~/.zshrc
% gc[Space] # expands to `git checkout`

# Add a session-specific abbreviation
% abbr -S x="git checkout"
% x[Space] # expands to `git checkout `
% source ~/.zshrc
% x[Space] # but only in the session it was created in

# Erase an abbreviation
% abbr -e gc
% gc[Space] # no expansion

# Add a global abbreviation
% abbr -g g=git
% echo global && g[Space] # expands to `echo global && git `

# Rename an abbreviation
% abbr -r gcm cm
% gcm[Space] # does not expand
% cm[Space] # expands to `git checkout main `

# Make the switch from aliases
% abbr import-aliases
% abbr import-git-aliases
```
