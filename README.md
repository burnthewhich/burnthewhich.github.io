# What's wrong with whichcraft?

The `which` command is a relic of a bygone Unix era that should have died a long time ago.

When I've suggested to greybeards that they should stop using `which`, they look at me like I'm the crazy one, and point out that "it's more portable than all the alternatives" (or just insult my parentage).

The problem with that statement is that it's true, but only in the sense of "it exists on every POSIX-compliant platform", not that you can rely on its behaviour.

Typically what they really mean is "it's portable to some ancient pre-POSIX version of Solaris", or "it's portable to BusyBox". OK, so if you care so much about portability to those crippled non-standard platforms that you're willing to forego portability to more standard platforms, you're welcome to go play in the cesspit on your own; have fun.

## What is which?

`which` is supposed to tell you which program would be run if you simply typed its name into the shell.
The thing it's not designed to do, but which is by far its most common use-case, is to tell a script what program would be invoked if the script invoked it by name later on.

The first thing to know about `which` is that it's a separate binary program, not part of whichever shell you're using, either interatively, or as the interpreter for your script. Therefore it does not _a priori_ know the internal state of your shell. This means it's always going to be an approximation.

A basic version of `which` will simply assume you want a program that's found in `$PATH`. Most of the time that'll be true, but this approach will forceably bypass anything that's _not_ an external binary:
* a more efficient built-in version of the same command;
* a shell function
* an exported shell function from the environment
* an alias (though that's probably a good thing)
* a better binary in a different location when PATH has been updated since you invoked `which cat`

A "smart" version will try to show you what will "really" happen, so it must
1. guess which shell you're running
2. read the startup files for that shell

The problem with that the shell is literally a guess; there's no reasonable mechanism for `which` to know the name of the program that invoked it. The best it can do is find out the interactive shell of the user from `getenv("SHELL")` or `getpwnam()`, which is not necessarily the interpreter of the script they're running.

And the problem with reading the startup files is that "reading" misleading. They're not configuration files where you simply "read" the "settings" from it; rather, they are full-blown scripts whose content has to be _run_ to compute and set the variables you're interested in, such as `$PATH` and `$FPATH`.

"But my `.profile` only contains assignments like `PATH=/usr/bin`. Well, that's great for you, but it's not true in general. People often have things like
* `PATH=$PATH:$HOME/bin:/usr/local/bin`
* `if [ -n "$foo" ]; then PATH=$foo/bin:$PATH ; fi`
* `for p in /here /there ~and /everywhere ; do case :$PATH: in *:$p/bin:*) ;; *) PATH=$PATH:$p/bin ; esac done`

And being scripts, they can contain _any_ commands, not just settings for `PATH`.
It's not unheard of for people to write things like `exec zsh -i` or `exec startx` inside `.profile` or `.tcshrc`.

## How to use "which" badly

You often see old scripts with long boilerplate like this:

```lang=sh
CAT=`which cat`
LS=`which ls`
TAR=`which tar`
TEST=`which test`
```
... going on _ad nauseam_ for every single command that the script wants to run. And then later on you'll see `$CAT` instead of just `cat`.

This is by far the most common - and least justifiable - use of the `which` command.
