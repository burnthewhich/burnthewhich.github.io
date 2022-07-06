# What's with `#!/usr/bin/env bash` ?

This business of using <tt>#!/usr/bin/env <em>whatever</em></tt> is getting out
of hand.

There is a widespread myth that it is more "portable" than
<tt>#!<em>/path/to/whatever</em></tt>, and that portability is all that matters.

Both of those are wrong.

In some cases it's simply _not_ more portable; in particular `/bin/sh` is _more_
widely available than `/usr/bin/env` (which, contrary to this myth, is _not_
available "everywhere").
> Footnote: some systems dispense with having `/bin` and instead install
everything in `/usr/bin`, but since they have a symlink from `/usr/bin` to `/bin`,
that means that `/bin/sh` is still valid. Other systems have neither `/bin` nor
`/usr/bin`, having instead `/system/bin`, so again you're no worse off by using
`/bin/sh`.

It is often suggested that <tt>#!/usr/bin/env <em>whatever</em></tt> is harmless
even when it provides no benefit.

In any given instance that may be true, but not always. The greater harm is in
normalizing it and using it unthinkingly everywhere. This inevitably winds up
with `env` being used in circumstances where it creates compatibility problems
or even security vulnerabilities.
> Footnote: The retort that "this doesn't affect security _on its own_" is
both true _and_ wrong: we have security breaches precisely because of assumptions
that things are safe _because nothing else stupid has been done_. The "best
practice" approach to security includes "defence in depth": many layers of guards
and locks, because some of them will inevitably be broken.

It was originally suggested as a fix for a specific problem, but somehow it's
morphed into a general purpose "portability" fix. And worse, it's started being
promoted as "best practice" in some circles, when it's anything but.

The "beneficiaries", if you can call them that, are the users who are too naïve
to edit the shebang line and put the correct path to the interpreter there.
Which means they're also too naïve to assess the security implications of what
they're doing.

The prime use-case for `env` was to support systems where the user does not have
the ability to install or replace software at a given path; for example MacOS
locks down `/bin/bash` to version 3 which is inadequate for many modern scripts.
