# Uninstalling

:::tip
These are the legacy v4 docs. For other versions, use the links in the header dropdown.
:::

Delete the session data storage directory

```shell:no-line-numbers
% rm -rf $ABBR_TMPDIR
```

If you want to delete the user abbreviations file,

```shell:no-line-numbers
% rm $ABBR_USER_ABBREVIATIONS_FILE
```

If you haven't customized `$ABBR_USER_ABBREVIATIONS_FILE`, you will probably want to delete its parent directory

```shell:no-line-numbers
# see if there's anything in there
% ls $ABBR_USER_ABBREVIATIONS_FILE:h
# IF you want to delete it
% rm -rf $ABBR_USER_ABBREVIATIONS_FILE:h
```

Then follow the standard uninstallation procedure for your installation method. This is typically the reverse of what you did to install.
