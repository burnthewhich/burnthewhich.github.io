# What's wrong with whichcraft?

## It's not portable

The main reason people stick with `which` instead of using better alternatives is because (they claim that) it's "more portable".

The problem with that assertion is that it's true, but only in the sense of "it exists on every POSIX-compliant platform", not in the sense that POSIX requires any useful behaviour.

So all that statement proves is that they haven't been paying attention for the last 30 years.
"More portable" might have been true in 1990. It's not true in 2021. Wake up and smell the POSIX daisies.

* Its output format is unspecified; it might or might not be just the bare path to the program.
* Its exit status is unspecified; it might reflect the existence of the program, or it might reflect whether the program can be executed by the current UID, GID or a supplementary GID, or it might reflect the syntactic validity, or it might just be random.
* It won't find a command that is a built-in, so it will "fail" if there isn't a program by the same name.
* Its performance is poor; why invoke an external program when a built-in can do the job faster _and_ better.
