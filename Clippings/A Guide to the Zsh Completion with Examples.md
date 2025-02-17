---
title: "A Guide to the Zsh Completion with Examples"
source: "https://thevaluable.dev/zsh-completion-guide-examples/"
author:
  - "[[Matthieu Cneude]]"
published: 2021-05-27
created: 2025-02-14
description: "How work the Zsh completion system and how to configure it according to your needs, without any framework or bloated library."
tags:
  - "clippings"
---
![The Zsh Completion System](https://thevaluable.dev/images/2021/zsh_completion_system/completion.jpg)

This article is part of a series about Zsh:

“The completion offered by Zsh is great, but could it be better? Why not trying to understand how it works? I could then configure it for my own needs!”

This was my thinking a couple of months ago, after waking up a Monday morning full of energy and will to eat my breakfast. I was young and innocent, unaware of the consequences of this thought making its nest in my brain. What could go wrong? After all, it’s only a completion system.

While reading the page of the Zsh manual concerning the completion, I saw the Demon of Complexity™ showing its nose. A dozen of questions were popping up, a twisted configuration syntax was unleashed, nonsensical descriptions were unfolding before my eyes.

When I see something complex, I can’t stop myself trying to simplify it. It took me hours to begin to understand this completion system, but now I think I’ve a good grasp on the Beast.

This article is the result of my efforts. It aims to explain the Zsh completion system in a simple way; more precisely, we’ll see:

- How to enable the Zsh completion.
- How does the completion system work.
- What’s the purpose of the zstyle module.
- How to use styles to configure the Zsh completion.
- What are the most useful styles we can use to customize the completion.
- How to trigger a precise completion using keystrokes.
- How to customize the completion menu using the module *complist*.
- What are the Zsh options we can set to configure the completion.

As I explain in a [previous article](https://thevaluable.dev/zsh-install-configure-mouseless/), I don’t use any Zsh framework (like oh-my-zsh or prezto) to have a full control on my config.

Enough rambling! Let’s dive into the fantastic and dangerous Zsh completion system.

## Enabling the Zsh Completion System

To initialize the completion for the current session, you need to *autoload* the function `compinit` and to call it. To do so, add the following in your file `.zshrc`:

```zsh
autoload -U compinit; compinit
```

If you wonder what’s this `autoload`, I already wrote about it [in this article](https://thevaluable.dev/zsh-install-configure-mouseless/).

You can also configure your completion with the shell command `compinstall`. It asks a series of question to decide what kind of completion you need, and it writes automatically your preferences in your `.zshrc`. In that case, you don’t need to autoload and run `compinit` manually.

But I find this tool very limited, and the questions it asks are quite obscure. I prefer writing my own config for the completion system in a separate file and source it in my `.zshrc`. More on that below.

It’s where things become slightly complicated. If you don’t care about the details, you can copy and paste [my simple config](https://github.com/Phantas0s/.dotfiles/blob/master/zsh/completion.zsh) in a file and source it in your `.zshrc`.

## How Does the Zsh Completion System Work

Zsh has two completion systems, the old *compctl* and the more recent *compsys*. We only look at compsys in this article.

Concretely, compsys is a collection of Zsh functions. You can find them in the [official repository of Zsh](https://github.com/zsh-users/zsh/tree/master/Completion). Three folders are specifically important:

- Base - The core of the completion system, defining the basic completers.
- Zsh - Functions for completing built-in Zsh commands (like `cd` for example).
- Unix - Function for completing external commands.

There are also folders for commands only available in some systems, like Solaris, Redhat, or Mandrive for example.

When you type a command in Zsh and hit the `TAB` key, a completion is attempted depending on the *context*. This context includes:

- What command and options have been already typed at the command-line prompt.
- Where the cursor is.

The context is then given to one or more *completer* functions which will attempt to complete what you’ve typed. Each of these completers are tried in order and, if the first one can’t complete the context, the next one will try. When the context can be completed, some possible *matches* will be displayed and you can choose whatever you want.

A warning is thrown if none of the completers are able to match the context.

There are two more things to know about completers:

- The names of completers are prefixed with an underscore, like `_complete` or `_expand_alias` for example.
- A few completers return a special value 0 which will stop the completion, even if more completers are defined afterward.

## Configuring Zsh Completion With zstyle

You can configure many aspects of the completion system using the zsh module *zstyle*.

Modules are part of Zsh but optional, detached from the core of the shell. They can be linked to the shell at build time or dynamically linked while the shell is running.

### What’s zstyle?

You might think that zstyle is used to configure some display styles, but it’s much more than that. It’s easier to see it as a flexible way to modify the default settings of Zsh scripts (modules, widgets, functions, and so on). The authors of these scripts need to define what are these settings, what *pattern* a user can use to modify them, and how these modifications can affect their code.

Here’s the general way you can use zstyle to configure a Zsh module:

```zsh
zstyle <pattern> <style> <values>
```

The *pattern* act as a namespace. It’s divided by colons `:` and each value between these colons have a precise meaning.

In the case of the Zsh completion system, the context we saw earlier (what you’ve already typed in your command-line) is compared with this pattern. If there’s a match, the style will be applied.

Don’t worry if all of that sounds confusing: bare with me, I’ll give some examples soon. For now, let’s look a bit closer to the zstyle patterns we can use to configure our completion system.

- `man zshcompsys` - Search for “Standard Styles”

### General zstyle Patterns for Completion

To configure the completion system, you can use zstyle patterns following this template:

```
:completion:<function>:<completer>:<command>:<argument>:<tag>
```

The substring separated with colons `:` are called *components*. Let’s look at the ones used for the completion system in details:

- `completion` - String acting as a namespace, to avoid pattern collisions with other scripts also using zstyle.
- `<function>` - Apply the style to the completion of an external function or widget.
- `<completer>` - Apply the style to a specific completer. We need to drop the underscore from the completer’s name here.
- `<command>` - Apply the style to a specific command, like `cd`, `rm`, or `sed` for example.
- `<argument>` - Apply the style to the nth option or the nth argument. It’s not available for many styles.
- `<tag>` - Apply the style to a specific tag.

You can think of a tag as a *type of match*. For example “files”, “domains”, “users”, or “options” are tags.

- `man zshcompsys` - Search for “Standard Tags”

You don’t have to define every component of the pattern. Instead, you can replace each of them with a star `*`. The more specific the pattern will be, the more precedence it will have over less specific patterns. For example:

```zsh
zstyle ':completion:*:*:cp:*' file-sort size
zstyle ':completion:*' file-sort modification
```

What happens if you set these styles?

1. If you hit `TAB` after typing `cp`, the possible files matched by the completion system will be ordered by size.
2. When you match files using the completion, they will be ordered by date of modification.

The pattern `:completion:*:*:cp:*` has precedence over `:completion:*` because it’s considered more precise.

The `*` replace any character *including the colon* `:`. That’s why the pattern `:completion:*:*:cp:*` is equivalent to `:completion:*:cp:*`. That said, I find the second form confusing: it’s not clear what `cp` is. Is it a command? A function? A tag? In that case it’s pretty obvious, but it’s not always `cp` in the pattern. Personally, I always try to use the first form.

You can run `zstyle` in your shell to display the styles set in your current session as well as their patterns.

### Examples of zstyles for the Zsh Completion

All of that is quite verbose and not very self-explanatory, so let’s look at more examples. Here’s a simple one:

```zsh
zstyle ':completion:*' verbose yes
```

This zstyle command is composed of:

- A pattern: `:completion:*`
- A style: `verbose`
- A value: `yes`

There is only one value given here, because the style `verbose` only accept one. But you can set more than one for some styles. Each of these values would be separated by a space. For example:

```
zstyle ':completion:*:*:cp:*' file-sort modification reverse
```

We give here two values to the style `file-sort`: `modification` (to order the matches by date of modification) and `reverse` (to reverse the order). As we saw before, the pattern `:completion:*:*:cp:*` indicates that we only set the style `file-sort` to the command `cp`.

Let’s see now a couple of styles we can set to improve our Zsh completion.

- `man zshmodules` - Search for “zstyle”

## Useful Style for the Zsh Completion System

I would recommend creating a new file `completion.zsh` somewhere and sourcing it directly in your file `.zshrc` as follows:

```zsh
source /path/to/my/completion.zsh
```

Every style defined here should be called after autoloading and calling compinit. If you don’t want a separate file, you can throw all this configuration into your `.zshrc` too.

### The Essential

#### Defining the Completers

Let’s first define the completer we’ll use for our completion system. Here are some interesting ones:

- `_complete` - This is the main completer we need to use for our completion.
- `_approximate` - This one is similar to `_complete`, except that it will try to correct what you’ve typed already (the context) if no match is found.
- `_expand_alias` - Expand an alias you’ve typed. It needs to be declared before `_complete`.
- `_extensions` - Complete the glob `*.` with the possible file extensions.

Note that you can use `_expand_alias` with the keystroke `CTRL+x a` by default, without the need to use it as a completer. Simply type one of your alias in Zsh and try to use the keystroke.

You need to set the zstyle `completer` to define the completer you want to use. The order matter: the completion system will try each of these completer one after the other. The completion stop when some matches are found or when the completer stop it by itself.

For example, here are the completers I use:

```zsh
zstyle ':completion:*' completer _extensions _complete _approximate
```

Since we’re dabbling into the completion system, the completer `_complete_help` might come in handy. You can use it as a function you can call with `CTRL+x h` by default.

When you’re not sure why you end up with some matches and not others, you can hit `CTRL+x h` (for `h`elp) before completing your command. It will display some information about what the completion system will do to complete your context.

#### Caching the Completion

Using a cache for the completion can speed up some commands, like `apt` for example. Let’s add the following in our file to enable it:

```zsh
zstyle ':completion:*' use-cache on
zstyle ':completion:*' cache-path "$XDG_CACHE_HOME/zsh/.zcompcache"
```

As you might have guessed, the style `cache-path` allows you to set the filename and location of the cache.

Instead of going through each match blindly to add what you want to your command, you can use a *completion menu*. For that, you need to set the style `menu` with the value `select` as follows:

```zsh
zstyle ':completion:*' menu select
```

You can also use this menu only if a precise `<number>` of matches is found with `select=<number>`. It’s even possible to start the menu selection only if the list of matches doesn’t fit the screen, by using the value `select=long`. Using both values `select=<number>` and `select=long` is possible too.

Having the values `select` and `interactive` allows you to filter the completion menu itself using the completion system. You can also configure a keystroke to switch this *interactive mode* when the completion menu is displayed. More on that later in this article.

Adding the value `search` to the style will allow you to fuzzy-search the completion menu. Again, a keystroke can be configured to have this functionality on demand.

### Formatting The Display

#### Colors and Decoration

Let’s now improve the display of our completion menu using the style `format`.

Each completer can define different *sequences* (beginning with `%`) for different tags. For example:

```zsh
zstyle ':completion:*:*:*:*:descriptions' format '%F{green}-- %d --%f'
```

As we saw quickly above, a tag is *most of the time* a type of match. More generally, you can see it as a type of information displayed during completion. Here, the `descriptions` tag is specific to the `format` style. It generates descriptions depending on the type of match. For example, if you have files displayed in the completion menu, the description for the tag “files” will be displayed too.

The value of `format` is used for displaying these descriptions. Here, we use in the style’s value the sequence `%d` which will be replaced by the description itself.

Here’s the result for local directories:

![Zsh format style with description for completion system](https://thevaluable.dev/images/2021/zsh_completion_system/format_description.png)

If you’re using the completer `_approximate`, you can set the format of the possible corrections too. For example:

```zsh
zstyle ':completion:*:*:*:*:corrections' format '%F{yellow}!- %d (errors: %e) -!%f'
```

Here’s the result:

![Zsh format style with correction for completion system](https://thevaluable.dev/images/2021/zsh_completion_system/format_correction.png)

To format messages or warnings (for example when no match is found), you can add the following:

```zsh
zstyle ':completion:*:messages' format ' %F{purple} -- %d --%f'
zstyle ':completion:*:warnings' format ' %F{red}-- no matches found --%f'
```

We can use the escape sequence `%F %f` in the style’s value to use a foreground color. Here’s a summary of sequences you can use:

- `%F{<color>} %f` - Change the foreground color with `<color>`.
- `%K{<color>} %k` - Change the background color with `<color>`.
- `%B %b` - Bold.
- `%U %u` - Underline.

For example, you can create a horrible display as follows:

```zsh
zstyle ':completion:*:descriptions' format '%U%K{yellow} %F{green}-- %F{red} %BNICE!1! %b%f %d --%f%k%u'
```

Your descriptions are now a piece of art.

#### Grouping Results

To group the different type of matches under their descriptions, you can add the following:

```zsh
zstyle ':completion:*' group-name ''
```

Without this style, all the descriptions will be at the top and the matches at the bottom:

![Before setting the style group-name in Zsh](https://thevaluable.dev/images/2021/zsh_completion_system/before_group.jpg)

With the value of this style set with an empty string, the matches will be grouped under the descriptions depending on their types:

![after setting the style group-name in Zsh](https://thevaluable.dev/images/2021/zsh_completion_system/after_group.jpg)

If you’re not satisfied by the order these descriptions are displayed, you can modify it too. For example:

```zsh
zstyle ':completion:*:*:-command-:*:*' group-order alias builtins functions commands
```

Here, `-command-` means any word in the “command position”. It means that we want the matches tagged `alias` to appear before `builtins`, `functions`, and `commands`.

#### Detailed List of Files and Folders

The style `file-list` can display the list of files and folder matched with more details, similar to the information you can display with `ls -l`.

For example:

```zsh
zstyle ':completion:*' file-list all
```

Here’s the result:

![Before setting the style group-name in Zsh](https://thevaluable.dev/images/2021/zsh_completion_system/long_list_file.jpg)

#### Colorful Completion List

To have nice colors for your directories and file in the completion menu, you can add the style `list-colors`.

```zsh
zstyle ':completion:*:default' list-colors ${(s.:.)LS_COLORS}
```

It will set the value with the content of the environment variable `LS_COLORS`, normally used by the command `ls --color=auto`.

You apparently need to have the module `zsh/complist` loaded, but it worked without it on my system. I describe a bit more the module complist later in this article.

You can configure the colors of any completion menu even further using the environment variable `ZLS_COLORS`.

- `man zshmodules` - Search “Colored completion listings”

### Smarter Completion System

#### Squeezing Slashes

By default, the completion system will expand `//` to `/*/`. For example, `cd ~//Documents` will be expanded to `cd ~/*/Documents`.

Usually, on Unix systems, `//` is expanded to `/`. If you prefer this behavior, you can set the style `squeeze-slashes` to true as follows:

```zsh
zstyle ':completion:*' squeeze-slashes true
```

#### Directory Stack Completion

For the command `cd`, `chdir` and `pushd`, you can use a hyphen `-` not only for options but for a directory stack entry.

By default, the Zsh completion system will try to complete for a directory stack entry when you hit tab after a hyphen `-` while using one of these commands. If you prefer completing for an option, you can set the style `complete-options` to true as follows:

```zsh
zstyle ':completion:*' complete-options true
```

If you’re interested to learn more about the directory stack, I described a nice way to navigate through it [in this article](https://thevaluable.dev/zsh-install-configure-mouseless/)

#### Sorting Matched Files

You can sort the files appearing in the completion menu as follows:

```zsh
zstyle ':completion:*' file-sort dummyvalue
```

If this style is not set (or set with a dummy value like in the example above), the files will be sorted alphabetically. You can use one of these values if you prefer another ordering:

- `size` - Order files by size.
- `links` - Order files depending on the links pointing to them.
- `modification` or `date` or `time` - Order files by date of modification.
- `access` - Order files by time of access.
- `change` or `inode` - Order files by the time of change.

You can also add `reverse` to the values to reverse the order. For example:

```zsh
zstyle ':completion:*' file-sort change reverse
```

If you add the value `follow`, the timestamp of the targets for symlinks will be used instead of the timestamp of the symlinks themselves.

#### Completion Matching Control

Setting the style `matcher-list` allows you to filter the matches of the completion with even more patterns.

For example, you can set this style for the completion to first try the usual completion and, if nothing matches, to try a case-insensitive completion:

```zsh
zstyle ':completion:*' matcher-list '' 'm:{a-zA-Z}={A-Za-z}'
```

The completion can also try to complete partial words you’ve typed with the following style:

```zsh
zstyle ':completion:*' matcher-list '' 'm:{a-zA-Z}={A-Za-z}' 'r:|[._-]=* r:|=*' 'l:|=* r:|=*'
```

This style would allow you, for example, to complete the file `_DSC1704.JPG` if you only try to complete its substring `1704`.

The patterns themselves are quite… obscure. If you want a good intellectual challenge, you can look at the manual. I wish you good luck.

- `man zshcompwid` - Search for “COMPLETION MATCHING CONTROL”

## Completion via Keystrokes

You can bind any completion style to a keystroke instead of using it with the general completion. For that, you need to use the completer `_generic`.

For example, if you want to expand aliases each time you hit `CTRL+a`, you can add the following lines:

```zsh
zle -C alias-expension complete-word _generic
bindkey '^a' alias-expension
zstyle ':completion:alias-expension:*' completer _expand_alias
```

You can replace `alias-expension` by the name of your choice. The behavior depends of the completer you use; for expanding aliases, we use `_expand_alias`. Feel free to use whatever completer you want for your own needs.

## The Module complist

I wrote already about the module “complist” [in this article](https://thevaluable.dev/zsh-install-configure-mouseless/). We saw there how to move around in the completion menu using the keys “hjkl”:

```zsh
zmodload zsh/complist
bindkey -M menuselect 'h' vi-backward-char
bindkey -M menuselect 'k' vi-up-line-or-history
bindkey -M menuselect 'j' vi-down-line-or-history
bindkey -M menuselect 'l' vi-forward-char
```

You can configure many more keystrokes related to the completion menu using these commands:

- `accept-line` - Validate the selection and leave the menu.
- `send-break` - Leaves the menu selection and restore the previous command.
- `clear-screen` - Clear the screen without leaving the menu selection.
- `accept-and-hold` - Insert the match in your command and let the completion menu open to insert another match.
- `accept-and-infer-next-history` - Insert the match and, in case of directories, open completion menu to complete its children.
- `undo` - Undo.
- `vi-forward-blank-word` - Move cursor to next group of match.
- `vi-backward-blank-word` - Move cursor to previous group of match.
- `beginning-of-buffer-or-history` - Move the cursor to the leftmost column.
- `end-of-buffer-or-history` - Move the cursor to the rightmost column.
- `vi-insert` - Toggle between normal and interactive mode. We’ve seen the interactive mode above.
- `history-incremental-search-forward` and `history-incremental-search-backward` - Begin incremental search.

For example, I’ve configured `CTRL+x i` to switch to the interactive mode in the completion menu:

```zsh
bindkey -M menuselect '^xi' vi-insert
```

- `man zshmodules` - Search for “THE ZSH/COMPLIST MODULE”

## Completion Options

You can also use options to modify the Zsh completion system. To set an option, you need to use the command `setopt`. For example:

Here’s a small selection of useful options for configuring the completion system:

- `ALWAYS_TO_END` - Always place the cursor to the end of the word completed.
- `LIST_PACKED` - The completion menu takes less space.
- `AUTO_MENU` - Display the completion menu after two use of the `TAB` key.
- `AUTO_COMPLETE` - Select the first match given by the completion menu. Override `AUTO_MENU`.
- `AUTO_PARAM_SLASH` - When a directory is completed, add a trailing slash instead of a space.
- `COMPLETE_IN_WORD` - By default, the cursor goes at the end of the word when completion start. Setting this will not move the cursor and the completion will happen on both end of the word completed.
- `GLOB_COMPLETE` - Trigger the completion after a glob `*` instead of expanding it.
- `LIST_ROWS_FIRST` - Matches are sorted in rows instead of columns.

- `man zshoptions` - Search for “Completion”

## Ready to Complete?

The Zsh completion system is a complex beast, but I tried to do my best to simplify it. I hope it didn’t cause any headache on your side.

What did we learn together in this article?

- The completion system go through a series of *completers* first. They’ll try to find matches depending on the context (the command you’ve typed) using different completion functions.
- The Zsh module “zstyle” allows you to configure settings for a specific Zsh module or widget. Don’t let the name `style` fools you: it can configure way more than visual styles.
- You can configure the Zsh completion system using options and the module complist, but using zstyle is the most flexible way to tune it following your craziest wishes.

As you see, the completion system of Zsh is far from being simple. I’ve covered some of the basics here, but there’s much more. Let me know if you want a follow-up article to dive even deeper in this madness.