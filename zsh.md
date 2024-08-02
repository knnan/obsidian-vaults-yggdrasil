`zsh-widgets`: synonymous with functions or commands
```zsh
zle -N <functionname>  # loads the function to zsh zsh-widgets 
bindkey '^ ' <functionname> # maps a shortcuts to trigger functionname
```

- `\e`, `\E`, = Escape
- `^[` = Alt key (on some keyboards this is the same as escape)
- `^?` = Delete
- `^X`, `^` = Control