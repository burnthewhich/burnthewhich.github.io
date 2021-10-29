# What's with `#!/usr/bin/env bash` ?

This business of using `#!/usr/bin/env` is getting out of hand.

It was originally suggested as a fix for a specific problem, but somehow it's
morphed into a general purpose "portability" fix. And worse, it's started being
promoted as "best practice" in some circles, when it's anything but.

The "beneficiaries", if you can call them that, are the users who are too naïve
to edit the shebang line and put the correct path to the interpreter there.
Which means they're also too naïve to assess the security implications of what
they're doing. (The retort that "this doesn't affect security _on its own_" is
both true and wrong: we have security breaches precisely because of assumptions
that things are safe _because nothing else stupid has been done_.)

In practice the main beneficiaries are Apple users, who cannot overwrite
`/bin/bash` with an updated version.

