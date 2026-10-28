# Limits

What direnv cannot do, and what it must
not be asked to do — the chapter the rest
of the guide has been promising.

## An .envrc is arbitrary code

Everything in this guide follows from one
fact, stated in chapter 1 and worth its
own heading here: the file direnv runs is
a **bash program**, with every capability
of the user who runs it. It can read every
file you can read, use your network, start
other programs, and — while it is loaded —
it has already finished running; the
environment it leaves behind is the least
of what it could do. The allow gate
(chapter 5) is therefore not a sandbox or
a review by direnv: direnv checks *that
you approved* the file, never *what the
file does*. There is no restricted mode,
no permission system inside an .envrc,
and no warning when an approved file
reads your documents — reading documents
is indistinguishable from legitimate work.
If a file's contents are beyond what you
can read and judge, the tool's honest
answer is the one the gate gives: leave
it blocked.

## Only hooked shells are served

direnv's world is the shells that carry
its hook, and the programs those shells
start. From that, three ordinary limits:

- **Programs launched elsewhere do not
  inherit.** A desktop menu, a file
  manager's "open with", another service
  manager: none passes through your shell,
  so a project's variables never reach
  them (chapter 8's workarounds cover
  editors; there is no general fix,
  because there is no moment where direnv
  is consulted).
- **Other people's shells are theirs.**
  direnv changes nothing for system
  services, scheduled jobs, or other
  users, and an .envrc is not a deployment
  mechanism. `direnv exec` (chapter 8) is
  the bridge for a job *you* start; a
  service that must have an environment
  should get it from its service manager's
  own configuration, with the .envrc as,
  at most, the human-readable source.
- **One shell, one folder.** A shell is
  in exactly one folder at a time, and
  direnv loads exactly that folder's
  environment. Two projects side by side
  means two shells — which most people
  find is the honest shape of the work
  anyway.

## It is not a safe for secrets

direnv *delivers* variables; delivery is
the whole job. A value in your
environment can be read by every program
you run — that is what environments are
for — so direnv provides no secrecy
inside the machine, and chapter 9's
pattern (secrets in a gitignored file the
.envrc loads) protects the repository,
not the running system. Machines where
the threat model includes other local
users or untrusted local programs need
real secret storage with real access
control, and a guide about that tool;
this one's job is only to keep you from
mistaking a convenience for a vault.

## The environment is a blunt instrument

Variables are global, untyped strings,
shared by everything in the session. Two
projects that want the same variable
differently are fine — only one loads at
a time — but two *tools in one project*
that want it differently have no direnv
answer. Nor does direnv mediate the
`PATH` wars of competing layouts: an
.envrc that layers several tools'
setups runs them in the order written,
and untangling an order problem is
reading the file, not a direnv setting.
And because the mechanism is a diff
against your shell, environments that
*you* mutate heavily by hand while inside
a project can drift from what the receipt
expects (chapter 12); direnv restores
its own changes faithfully, not your
whole session's history.

## Platform edges

The lab ran direnv on Linux with bash.
The project's own documentation is the
authority beyond that: hooks exist for
several shells (chapter 3), and direnv
works on macOS and in Windows' Unix-like
environments, but a native Windows shell
is not its home ground, and editor and
terminal integrations vary by platform.
Behaviour this guide verified is claimed
for the lab's ground only; elsewhere the
shape holds and the details are the
documentation's to state.
