# What's wrong with whichcraft?

## How to use "which" (very) badly

You often see old scripts with long boilerplate like this:

```lang=sh
CAT=`which cat`
LS=`which ls`
TAR=`which tar`
TEST=`which test`
```
... going on _ad nauseam_ for every single command that the script will later want to run. And then later on you'll see `$CAT` instead of just `cat`.

This is by far the most common - and least justifiable - use of the `which` command in scripts.

The first clue is the use of <tt>&#x60;backticks&#x60;</tt> instead of `$(parentheses)`. These show that's you're looking at _old_ code. Or code written by someone who's learned from _ancient_ sources. Or the output of GNU AutoConf (which itself pre-dates POSIX).

There are additional the problems that are "co-effects" of the coding style, rather than directly caused by `which`:
* The lack of quotes around variable expansions means that if, say, `cat` is located in a directory that has a space in its name, `$CAT` will _not_ run the intended command.
* The result of each `which` isn't checked, so if the command doesn't exist, then instead of reporting `cat: command not found`, you get the first argument invoked as a command. That usually fails, but sometimes it will do something spectacularly wrong.

Lastly, there's the stylistic horror of storing code inside variables, which drastically reduces the efficacy of automated code analysis.
