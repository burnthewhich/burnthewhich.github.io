# What's wrong with whichcraft?

The `which` command is a relic of a bygone Unix era that should have died a long time ago.

When I've suggested to "experienced programmers" that they should stop using `which`, and use something more reliable, they look at me like I'm the crazy one, and point out that "[it's more portable than all the alternatives](which/unportable)" (or just insult my parentage).

Typically what they really mean is "it's portable to some ancient pre-POSIX version of Solaris", or "it's portable to BusyBox". OK, so if you care so much about portability to those crippled non-standard platforms that you're willing to forego portability to more standard platforms, you're welcome to [go play in the cesspit](which/broken-shells) on your own; have fun.

## What is `which`?

`which` is supposed to tell you which program would be run if you simply typed its name into the shell. What it actually does is tell you where it might find the command in `$PATH`, which means it's OK for interactive use as long as you understand that limitation.

The thing it's not designed to do, but which is by far its most common use-case, is to tell a script what program would be invoked by the script if it invoked the program by name later on. POSIX does not require the output of `which` to be usable for this or any particular purpose; merely that it "indicates" the path of the program corresponding to each argument.

## How do I stop using it?

There are broadly two reasons why people use `which` in their scripts.

1. The "good" reason is simply to check, before starting, that all the required commands will be available.
2. The "bad" reason is to [grab the path to each program](which/stashing), and use that -- instead of the bare command name -- to [invoke it](which/stashing).

The fix to the first reason is to use `command -v`, because POSIX requires it to have a useful exit status. (And it also requires its output to be exactly the path to the program, no more, no less.)

The fix to the second reason is simply: don't.<br/>
Don't write `CAT=`&#x60;`which cat`&#x60; and then `$CAT`.<br/>
Simply writing `cat` will work just as well, just as fast, and _more_ reliably than `$CAT`.

## But what's the problem?

The first thing to know about `which` is that it's a separate binary program, not part of whichever shell you're using, either interactively, or as the interpreter for your script. Therefore it does not _a priori_ know the internal state of your shell, and _that_ means it's always going to be an approximation.

A basic version of `which` will simply assume you want a program that's found in `$PATH`. Most of the time that'll be true, but this approach will forceably bypass anything that's _not_ an external binary:
* a more efficient built-in version of the same command;
* a shell function
* an exported shell function from the environment
* an alias (though that's arguably a good thing)

It will also fail to take into account things that might change between when you invoke `which` and when you actually want to invoke the command:
* `PATH` can be changed
* the current directory can be changed (which matters if `PATH` includes a relative path.
* a new program might be installed, or an old program might be removed
* a loadable shell module could be loaded or unloaded

There are also [enhanced versions](which/too-smart) of `which` that have even more problems.
