# Commands

:::tip
These are the legacy v4 docs. For other versions, use the links in the header dropdown.
:::

zsh-abbr has commands to add, rename, and erase abbreviations; to add abbreviations for every alias or Git alias; to list the available abbreviations with or without their expansions; and to create aliases from abbreviations.

`abbr` with no arguments is shorthand for `abbr list`. `abbr ...` with arguments is shorthand for `abbr add ...`.

[[toc]]

## `add`

```shell:no-line-numbers
abbr [(add | -a)] [<SCOPE>] [<TYPE>] [--dry-run] [(--quiet | --quieter)] [--force] ABBREVIATION=EXPANSION
```

Add a new abbreviation.

To add a session abbreviation, use the **--session** or **-S** scope flag. Otherwise, or if the **--user** or **-U** scope flag is used, the new abbreviation will be available to all sessions.

To add a global abbreviation, use the **--global** flag. Otherwise, the new abbreviation will be a command abbreviation.

```shell:no-line-numbers
% abbr add gcm='git checkout main'
% gcm[Space] # expands as git checkout main
% gcm[Enter] # expands and accepts git checkout main
```

`add` is the default command, and does not need to be explicit:

```shell:no-line-numbers
% abbr gco='git checkout'
% gco[Space] # expands as git checkout
% gco[Enter] # expands and accepts git checkout
```

The ABBREVIATION must be only one word long.

As with aliases, to include whitespace, quotation marks, or other special characters like `;`, `|`, or `&` in the EXPANSION, quote the EXPANSION or `\`-escape the characters as necessary.

```shell:no-line-numbers
abbr a=b\;c  # allowed
abbr a="b|c" # allowed
```

User-scope abbreviations can also be manually to the user abbreviations file. See **Storage** below.

The session regular, session global, user regular, and user global abbreviation sets are independent. If you wanted, you could have more than one abbreviation with the same ABBREVIATION. Order of precedence is "session command > user command > session global > user global".

Use `--dry-run` to see what would result, without making any actual changes.

Will error rather than overwrite an existing abbreviation.

Will warn if the abbreviation would replace an existing command. To add in spite of the warning, use `--force`. To silence the warning, use `--quieter`.

## `clear-session`

```shell:no-line-numbers
abbr (clear-session | c)
```

Erase all session abbreviations.

## `erase`

```shell:no-line-numbers
abbr (erase | e) [<SCOPE>] [<TYPE>] [--dry-run] [--quiet] ABBREVIATION
```

Erase an abbreviation.

Use the **--session** or **-S** scope flag to erase a session abbreviation. Otherwise, or if the **--user** or **-U** scope flag is used, a cross-session abbreviation will be erased.

Use the **--global** flag to erase a session abbreviation. Otherwise, a cross-session abbreviation will be erased.

```shell:no-line-numbers
% abbr gcm="git checkout main"
% gcm[Enter] # expands and accepts git checkout main
Switched to branch 'main'
% abbr -e gcm;[Enter] # or abbr -e gcm[Ctrl-Space][Enter]
% gcm[Space|Enter] # normal
```

User abbreviations can also be manually erased from the `ABBR_USER_ABBREVIATIONS_FILE`. See **Storage** below.

## `expand`

```shell:no-line-numbers
abbr (expand | x) ABBREVIATION
```

Output the ABBREVIATION's EXPANSION.

```shell:no-line-numbers
% abbr gc="git checkout"
% abbr -x gc; # or `abbr -x gc[Ctrl-Space][Enter]`
git checkout
```

## `export-aliases`

```shell:no-line-numbers
abbr export-aliases [<SCOPE>] [<TYPE>]
```

Export abbreviations as alias commands. Regular abbreviations follow global abbreviations. Session abbreviations follow user abbreviations.

Use the **--session** or **-S** scope flag to export only session abbreviations. Use the **--user** or **-U** scope flag to export only user abbreviations.

Use the **--global** or **-g** type flag to export only global abbreviations. Use the **--regular** or **-r** type flag to export only regular abbreviations.

Combine a scope flag and a type flag to further limit the output.

```shell:no-line-numbers
% abbr gcm="git checkout main"
% abbr -S g=git
% abbr export-aliases
alias gcm='git checkout main'
% abbr export-aliases --session
alias g='git'
```

## `help`

```shell:no-line-numbers
abbr (help | --help)
```

Show the manpage.

## `import-aliases`

```shell:no-line-numbers
abbr import-aliases [<type>] [--dry-run] [--quiet]
```

Add regular abbreviations for every regular alias in the session, and global abbreviations for every global alias in the session.

```shell:no-line-numbers
% cat ~/.zshrc
# --snip--
alias -S d='bin/deploy'
# --snip--

% abbr import-aliases
% d[Space] # expands to bin/deploy
```

Note that zsh-abbr does not lint the imported abbreviations. An effort is made to correctly wrap the expansion in single or double quotes, but it is possible that importing will add an abbreviation with a quotation mark problem in the expansion. It is up to the user to double-check the result before taking further actions.

Use `--dry-run` to see what would result, without making any actual changes.

## `import-fish`

```shell:no-line-numbers
abbr import-fish [<SCOPE>] FILE [--dry-run] [--quiet]
```

Import fish abbr-syntax abbreviations (`abbreviation expansion` as compared to zsh abbr's `abbreviation=expansion`).

In fish:

```shell:no-line-numbers
abbr -s > file/to/save/fish/abbreviations/to
```

Then in zsh:

```shell:no-line-numbers
abbr import-fish file/to/save/fish/abbreviations/to
# file is no longer needed, so feel free to
# rm file/to/save/fish/abbreviations/to
```

Note that zsh-abbr does not lint the imported abbreviations. An effort is made to correctly wrap the expansion in single or double quotes, but it is possible that importing will add an abbreviation with a quotation mark problem in the expansion. It is up to the user to double-check the result before taking further actions.

Use `--dry-run` to see what would result, without making any actual changes.

## `import-git-aliases`

```shell:no-line-numbers
abbr [--dry-run] [--quiet] import-git-aliases [--file <config-file>]
```

Add two abbreviations for every Git alias available in the current session: a global abbreviation where the WORD is prefixed with `g`, and a command abbreviation. For both the EXPANSION is prefixed with `git[Space]`.

Use `--file <config-file>` to use a config file instead of the one specified by GIT_CONFIG (see `man git-config`).

Use the **--session** or **-S** scope flag to create session abbreviations. Otherwise, or if the **--user** or **-U** scope flag is used, the Git abbreviations will be user.

```shell:no-line-numbers
% git config alias.co checkout

# session
% abbr import-git-aliases -S
% gco[Space] # git checkout
% echo gco[Space] # echo git checkout
% co[Space] # git checkout
% echo co[Space] # echo co
% source ~/.zshrc
% gco[Space] # gco

# user
% abbr import-git-aliases
% gco[Space] # git checkout
% source ~/.zshrc
% gco[Space] # git checkout
```

Note for users migrating from Oh-My-Zsh: [OMZ's Git aliases are shell aliases](https://github.com/ohmyzsh/ohmyzsh/blob/master/plugins/git/git.plugin.zsh), not aliases in the Git config. To add abbreviations for them, use **import-aliases**.

Note that zsh-abbr does not lint the imported abbreviations. It is up to the user to double-check the result before taking further actions.

Use `--dry-run` to see what would result, without making any actual changes.

## `list`

```shell:no-line-numbers
abbr [list] [<SCOPE>] [<TYPE>]
```

List the abbreviations with their expansions, like zsh's `alias`. Regular abbreviations follow global abbreviations. Session abbreviations follow user abbreviations.

```shell:no-line-numbers
% abbr a=apple
% abbr -g b=ball
% abbr -S c=cat
% abbr -S -g d=dog
% abbr list
a="apple"
b="ball"
c="cat"
d="dog"
% source ~/.zshrc
% abbr list
a="apple"
b="ball"
```

`list` is the default when no additional arguments are passed; it does not need to be made explicit:

```shell:no-line-numbers
% abbr a=apple
% abbr
a="apple"
```

## `list-abbreviations`

```shell:no-line-numbers
abbr (list-abbreviations | l) [<SCOPE>] [<TYPE>]
```

List the abbreviations only, like fish's `abbr -l`. Regular abbreviations follow global abbreviations. Session abbreviations follow user abbreviations.

```shell:no-line-numbers
% abbr a=apple
% abbr -g b=ball
% abbr -S c=cat
% abbr -S -g d=dog
% abbr list-abbreviations
a
b
c
d
% source ~/.zshrc
% abbr list-abbreviations
a
b
```

## `list-commands`

```shell:no-line-numbers
abbr (list-commands | L) [<SCOPE>] [<TYPE>]
```

List as commands suitable for export, like zsh's `alias -L`. Regular abbreviations follow global abbreviations. Session abbreviations follow user abbreviations.

```shell:no-line-numbers
% abbr a=apple
% abbr -g b=ball
% abbr -S c=cat
% abbr -S -g d=dog
% abbr list-abbreviations
abbr a="apple"
abbr -g b="ball"
abbr -S c="cat"
abbr -S -g d="dog"
% source ~/.zshrc
% abbr list-abbreviations
abbr a="apple"
abbr -g b="ball"
```

## `profile`

```shell:no-line-numbers
abbr profile
```

Log profile information for debugging.

## `rename`

```shell:no-line-numbers
abbr (rename | R) [<SCOPE>] [<TYPE>] [--dry-run] [(--quiet | --quieter)] OLD NEW
```

Rename an abbreviation.

Use the **--session** or **-S** scope flag to rename a session abbreviation. Otherwise, or if the **--user** or **-U** scope flag is used, a cross-session abbreviation will be renamed.

Use the **--global** flag to rename a global abbreviation. Otherwise, a command abbreviation will be renamed.

Rename is scope- and type-specific. If you get a "no matching abbreviation" error, make sure you added the right flags (list abbreviations if you are not sure).

```shell:no-line-numbers
% abbr add gcm git checkout main
% gcm[Space] # expands to git checkout main
% gm[Space] # no expansion
% abbr rename gcm[Ctrl-Space] gm
% gcm[Space] # no expansion
% gm[Space] # expands to git checkout main
```

Use `--dry-run` to see what would result, without making any actual changes.

Abbreviations can also be manually renamed in the `ABBR_USER_ABBREVIATIONS_FILE`. See **Storage** below.

Conflicts will error or warn. See **add** for details.

## `version`

```shell:no-line-numbers
abbr (version | --version | -v)
```

Show the current version.
