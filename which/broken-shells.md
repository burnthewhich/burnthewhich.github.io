# What's wrong with whichcraft?

## But what about Solaris or Busybox?

I make it a point of principle not to litter my entire codebase with hacks to accommodate stupid broken systems.
If I must deal with them, I refactor the code so that their fix-ups are isolated.

If your shell is so old or broken that it doesn't have `command -v`, firstly consider upgrading it.

Secondly, consider using shell functions to hide the brokenness.
```lang=sh
if command -v sh >/dev/null 2>&1 ; then
  # We're in a proper POSIX shell
  i_need() {
    command -v "$1" >/dev/null 2>&1 || {
      echo "Can't find required subprogram $1 - aborting"
      exit 1
    }
  }
else
  # We're in busybox or some other broken shell
  i_need() {
    __can_do=$( which -- "$1" 2>&1 ) &&
    case $__can_do in
       */"$1")  : ;;
       *) echo "Can't find required subprogram $1 - aborting"
          exit 1
    esac
  }
fi

i_need cat
i_need id
i_need tar
i_need test
```

Not only does this avoid cluttering the whole program with variables, but the function name helps document the purpose of the checks.

## But what about broken built-ins?

Ok, sometimes you really do want the external program rather than the built-in.

Again, shell functions come to the rescue:
```lang=sh
if (put your test here, to see if the built-in fubar is broken) ; then
  fubar() {
    command fubar "$@"
  }
fi
```
