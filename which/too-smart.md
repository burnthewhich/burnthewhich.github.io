# What's wrong with whichcraft?

## Too smart for its own good.

Some older versions of `which` try to be "smart", to show you what will "really" happen.

Firstly, that just kills any assumptions of portability, and it certainly kills any notion that the output will be "the path to the program that would be invoked", when it might instead say "foo is an alias" or "test is a built-in".

And secondly, actually implementing that means that it must:
1. guess which shell is running, and
2. read the startup files for that shell

And when I say "guess", I mean quite literally a guess (on the part of the programmer who wrote that version of `which`);
there's no reasonable mechanism for `which` to know the name of the program that invoked it.

Some versions of `which` allow you to specify the shell, as an extra option, but yet again that kills portability.

The best it can find out is the interactive shell of the user, using `getenv("SHELL")` or `getpwnam()`.

Of course, that is not necessarily the interpreter of the script that's running.

But even when it _does_ guess the right shell, "reading the startup files" is misleading.

These are not configuration files where you simply "read" the "settings" from then;
rather, they are full-blown scripts whose content has to be _run_ to compute and set the variables you're interested in, including `$PATH`.

Some folk might say …
> but my `~/.profile` only contains assignments like `PATH=/usr/bin`

… which is great for them, but not true in general, and in practice
`~/.profile` oftens contains things like:
* `PATH=$PATH:$HOME/bin:/usr/local/bin`
* `if [ -n "$foo" ]; then PATH=$foo/bin:$PATH ; fi`
* ```
  for p in /here /there ~and /everywhere ; do
    case :$PATH: in
      *:$p/bin:*) ;;
      *) PATH=$PATH:$p/bin
    esac
  done
  ```
* `tset`
* `. /etc/bash.bashrc`

And being scripts, they can contain _any_ commands, not just settings for `PATH`;
it's not unheard of for people to write things like `tset` or `exec zsh -i` or `exec startx` inside `.profile` or `.login`.

Good luck "reading" the "settings" from those without resorting to invoking an actual shell to run the file and see what it does.
