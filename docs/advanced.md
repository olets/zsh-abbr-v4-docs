# Advanced

:::tip
These are the legacy v4 docs. For other versions, use the links in the header dropdown.
:::

[[toc]]

## Configuration variables

In addition to the following, setting `NO_COLOR` (regardless of its value) will disable color output. See https://no-color.org/.

Variable | Type | Use | Default
---|---|---|---
`ABBR_AUTOLOAD` | integer | If non-zero, automatically account for updates to the user abbreviations file (see [Storage and manual editing](#storage-and-manual-editing)) | 1
`ABBR_DEBUG` | integer | If non-zero, print debugging messages | 0
`ABBR_DEFAULT_BINDINGS` | integer | If non-zero, add the default bindings (see [Bindings](#bindings)) | 1
`ABBR_DRY_RUN` | integer | If non-zero, use dry run mode without passing `--dry-run` | 0
`ABBR_FORCE` | integer | If non-zero, use force mode without passing `--force` (see [`add`](#add)) | 0
`ABBR_PRECMD_LOGS` | integer | If non-zero, support precmd logs, for example to warn that a deprecated widget was used | 1
`ABBR_QUIET` | integer | If non-zero, use quiet mode without passing `--quiet` | 0
`ABBR_QUIETER` | integer | If non-zero, use quieter mode without passing `--quieter` | 0
`ABBR_TMPDIR` | String | Path to the directory temporary files are stored in. _Ends in `/`_ | `${${TMPDIR:-/tmp}%/}/zsh-abbr/` *
`ABBR_USER_ABBREVIATIONS_FILE` | String | Path to the file user abbreviation are stored in (see [Storage and manual editing](#storage-and-manual-editing)) | `$XDG_CONFIG_HOME/zsh/abbreviations` if you have `XDG_CONFIG_HOME` defined\*\*, otherwise `$HOME/.config/zsh/abbreviations` \*\*\*

\* If changing this, you may want to delete the default directory.

\*\* Unless you've been using zsh-abbr without a customized `ABBR_USER_ABBREVIATIONS_FILE` since before `XDG_CONFIG_HOME` support was added (v4.8.0). In that case zsh-abbr will use the pre-4.8.0 `$HOME/.config/zsh/abbreviations`.

\*\*\* If changing this, you may want to delete the default file.

## Exported variables

In addition to exporting the configuration variables above, zsh-abbr creates the following variables:

Variable | Type | Value
---|---|---
`ABBR_GLOBAL_SESSION_ABBREVIATIONS` | associative array | The global session abbreviations
`ABBR_GLOBAL_USER_ABBREVIATIONS` | associative array | The global user abbreviations
`ABBR_INITIALIZING` | integer | Set to `1` when zsh-abbr is initializing
`ABBR_LOADING_USER_ABBREVIATIONS` | integer | Set to `1` when the interactive shell is refreshing its list of user abbreviations, otherwise not set
`ABBR_PRECMD_MESSAGE` | prompt string | Message shown by `precmd` hook if `ABBR_PRECMD_LOGS` is non-zero
`ABBR_REGULAR_SESSION_ABBREVIATIONS` | associative array | The regular session abbreviations
`ABBR_SOURCE_PATH` | string | Path to the `zsh-abbr.zsh`
`ABBR_REGULAR_USER_ABBREVIATIONS` | associative array | The regular user abbreviations

Each element in `ABBR_GLOBAL_SESSION_ABBREVIATIONS`, `ABBR_GLOBAL_USER_ABBREVIATIONS`, `ABBR_REGULAR_SESSION_ABBREVIATIONS`, and `ABBR_REGULAR_USER_ABBREVIATIONS` has the form `ABBREVIATION=EXPANSION`. The expansion value is quoted. Scripters will probably want to remove one level of quotes, using the [Q modifier](http://zsh.sourceforge.net/Doc/Release/Expansion.html#Modifiers) (e.g. `for v in ${(Qv)ABBR_REGULAR_USER_ABBREVIATIONS}...`).

## Storage and manual editing

User abbreviations live in a plain text file which you can edit directly, share, keep in version control, etc. Abbreviations in this file are loaded when each new session is opened; non-`abbr` commands will be ignored and then excised from the file.

zsh-abbr automatically keeps the user abbreviations storage file alphabetized, with all global user abbreviations before the first regular user abbreviation.

Every time an `abbr` command is run, the session's updates its user abbreviations with the latest from the user abbreviations file. This should add no appreciable time, but you prefer it can be turned off by setting `ABBR_AUTOLOAD=0`.

To refresh the user abbreviations from the user abbreviation, run `abbr load` (or any other `abbr` command).

## Bindings

By default

- <kbd>Space</kbd> expands abbreviations
- <kbd>Ctrl</kbd><kbd>Space</kbd> is a normal space
- <kbd>Enter</kbd> expands and accepts abbreviations

(In incremental search mode, <kbd>Space</kbd> is a normal space and <kbd>Ctrl</kbd><kbd>Space</kbd> expands abbreviations.)

There are three available widgets:

Widget | Behavior | Default binding
---|---|---
`abbr-expand` | If following an abbreviation, expands it.<br> Replaces deprecated `_abbr_expand_widget` | Not bound
`abbr-expand-and-accept` | If following an abbreviation, expands it; then accepts the line.<br> Replaces deprecated `_abbr_expand_and_accept` | <kbd>Enter</kbd>
`abbr-expand-and-space` | If following an abbreviation, expands it; then adds a space.<br> Replaces deprecated `_abbr_expand_and_space` | <kbd>Space</kbd>

In the following example, additional bindings are added such that <kbd>Ctrl</kbd><kbd>e</kbd> expands abbreviations without adding a trailing space and <kbd>Ctrl</kbd><kbd>a</kbd> has the same behavior as <kbd>Space</kbd>.

```shell:no-line-numbers
% cat ~/.zshrc
# -- snip --
bindkey "^E" abbr-expand
bindkey "^A" abbr-expand-and-space
# -- snip --
```

To prevent the creation of the default bindings, set `ABBR_DEFAULT_BINDINGS` to `0` before initializing zsh-abbr. In the following example, <kbd>Ctrl</kbd><kbd>Space</kbd> expands abbreviations and <kbd>Space</kbd> is not bound to any zsh-abbr widget.

```shell:no-line-numbers
% cat ~/.zshrc
# -- snip --
ABBR_DEFAULT_BINDINGS=0
bindkey "^ " abbr-expand-and-space
# -- snip --
# load zsh-abbr
# -- snip --
```

## Highlighting

[fast-syntax-highlighting](https://github.com/zdharma/fast-syntax-highlighting) users see [#24](https://github.com/olets/zsh-abbr/issues/24).

To highlight user abbreviations that will expand, [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) users can add these lines to `.zshrc` *below* where zsh-abbr is loaded. For more info see the [zsh-syntax-highlighting regexp highlighter documentation](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/docs/highlighters/regexp.md).

Replace `<styles for global abbreviations>` with a [zsh character highlighting](http://zsh.sourceforge.net/Doc/Release/Zsh-Line-Editor.html#Character-Highlighting) string (start at "The available types of highlighting are the following."). For example `fg=blue`, `fg=blue,bg=red,bold`, etc.

Linux:

```shell:no-line-numbers
ZSH_HIGHLIGHT_HIGHLIGHTERS+=(regexp)
ZSH_HIGHLIGHT_REGEXP+=('^[[:blank:][:space:]]*('${(j:|:)${(k)ABBR_REGULAR_USER_ABBREVIATIONS}}')$' <styles for regular abbreviations>)
ZSH_HIGHLIGHT_REGEXP+=('\<('${(j:|:)${(k)ABBR_GLOBAL_USER_ABBREVIATIONS}}')$' <styles for global abbreviations>)
```

macOS:

```shell:no-line-numbers
ZSH_HIGHLIGHT_HIGHLIGHTERS+=(regexp)
ZSH_HIGHLIGHT_REGEXP=('^[[:blank:][:space:]]*('${(j:|:)${(k)ABBR_REGULAR_USER_ABBREVIATIONS}}')$' <styles for regular abbreviations>)
ZSH_HIGHLIGHT_REGEXP+=('[[:<:]]('${(j:|:)${(k)ABBR_GLOBAL_USER_ABBREVIATIONS}}')$' <styles for global abbreviations>)
```

## vi mode compatibility

Switching to vi mode —with plain old `bindkey -v` or with plugin vi/Vim mode plugin that calls `bindkey -v` — will wipe out the keybindings zsh-abbr's interactive behavior relies on. If you use vi mode, enable it before initializing zsh-abbr. For example, the simplest `.zshrc` for a zinit user would be

```shell:no-line-numbers
bindkey -v
zinit light olets/zsh-abbr
```

## macOS System Text Substitutions

The following snippet will make your global macOS text substitutions available in the shell.

```shell:no-line-numbers
for substitution in ${(f)"$(defaults read ~/Library/Preferences/.GlobalPreferences.plist NSUserDictionaryReplacementItems | plutil -convert json -o - - | jq -r 'to_entries[] | "\(.value.replace)=\(.value.with)"')"}; do
  abbr add [options] "$substitution"
done
```
