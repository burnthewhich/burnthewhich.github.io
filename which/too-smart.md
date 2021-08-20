# What's wrong with whichcraft?

## Too smart for its own good.

Some older versions of `which` try to be "smart", to show you what will "really" happen.

Firstly, that just kills any assumptions of portability, and it certainly kills any notion that the output will be "the path to the program that would be invoked", when it might instead say "foo is an alias" or "test is a built-in".

And secondly, actually implementing that means that it must:
1. guess which shell you're running, and
2. read the startup files for that shell

And when I say "guess", I mean quite literally a guess (on the part of the programmer who wrote that version of `which`);
there's no reasonable mechanism for `which` to know the name of the program that invoked it.

Some versions of which allow you to specify the shell, as an extra option, but that yet again kills portability.

The best it can do is find out the interactive shell of the user from `getenv("SHELL")` or `getpwnam()`.

Of course, that is not necessarily the interpreter of the script they're running.

But even it is is the right shell, "reading the startup files" is misleading.

These are not configuration files where you simply "read" the "settings" from then;
rather, they are full-blown scripts whose content has to be _run_ to compute and set the variables you're interested in, such as `$PATH`.

"But my `~/.profile` only contains assignments like `PATH=/usr/bin`. Well, that's great for you, but it's not true in general.

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
Good luck "reading" the "settings" from those.

And being scripts, they can contain _any_ commands, not just settings for `PATH`.
It's not unheard of for people to write things like `exec zsh -i` or `exec startx` inside `.profile` or `.login`.

