# Beyond the Prompt

Outcome: direnv working where there is no
prompt — one command run with a project's
environment on demand — and an honest
account of the integrations that live
outside the terminal.

The hook serves shells. But plenty of
work starts elsewhere: a script run by a
scheduler, a build tool that wants one
command executed "as if inside the
project", an editor that should see the
project's variables. direnv's answer to
the first two is a subcommand; to the
third, other people's integrations, which
this chapter describes but did not run.

## direnv exec — borrow an environment once

```bash
direnv exec <folder> <command> [args…]
```

loads the approved .envrc found in (or
above) the named folder, runs the command
with that environment, and exits — your
shell untouched. The lab ran its sample
project's script from home, where the
project's variables are provably absent
(transcript
`direnv-lab/transcripts/t07-exec.txt`):

```text
lab$ echo "FIELDKIT_ENV is [$FIELDKIT_ENV]"
FIELDKIT_ENV is []
lab$ direnv exec ~/projects/fieldkit fieldkit-hello
direnv: loading ~/projects/fieldkit/.envrc
fieldkit running in development mode on port 9090
lab$ echo "FIELDKIT_ENV is [$FIELDKIT_ENV]"
FIELDKIT_ENV is []
```

Everything chapter 4 did with `PATH_add`
came along: `fieldkit-hello` was findable
by the borrowed environment, ran with the
project's values, and afterwards the
shell could not find it and held none of
the variables. Note that `exec` honours
the gate exactly as the hook does — an
unapproved or changed .envrc is not run
for a one-off command either. That makes
`exec` the right shape for scheduled jobs
and build scripts that need a project's
world for a single step: the command gets
the environment, nothing accumulates, and
the approval discipline still applies.

## direnv export — the environment as text

The hook itself is built on `direnv
export bash`: direnv prints, as shell
text, the diff for the current folder.
Other consumers use the other spellings
of the same command — `direnv export`
supports output for several shells, for
JSON, for GitHub Actions' environment
format, and for systemd — so a service
file or a CI step can receive the
project's environment in its own dialect.
The lab's verified surface here is the
bash export the hook evaluates at every
prompt (chapters 2 and 4); the other
dialects are listed from the command's
help and are documented-only in this
guide. One use is worth naming even
unrun, because it is the common one:
editors and IDEs that can be told to run
a command to obtain an environment can be
pointed at `direnv export json` in the
project folder, giving the editor the
same variables your shell gets.

## Editor integrations

Editors meet direnv through extensions —
there is a widely used one for VS Code,
and community integrations for Emacs and
Vim among others — which arrange for the
editor's own processes (language servers,
terminals, debuggers) to inherit the
project environment, roughly by the
`export` route above. None was run in
this guide's lab: the lab is a terminal,
and an editor's behaviour cannot be
transcripted the way a shell's can. What
can be said from the shell side, and
matters more: an editor **launched from a
loaded shell** inherits that shell's
environment like any child process —
`cd` into the project, let it load, then
start the editor from that prompt, and
its terminals and tasks see the project's
world with no extension at all. Launch
the same editor from a desktop menu and
it sees the desktop's environment
instead, which is chapter 11's limit, not
a bug in either program.

## direnv reload and the manual override

Two small commands complete the surface.
`direnv reload` forces the current
folder's environment to be unloaded and
loaded again at the next prompt — the
tool for when you have changed something
the watch list does not cover, or simply
want the load messages to re-run so you
can read them. And everything the hook
does can be overridden for a single
command without direnv's help at all:
`FIELDKIT_PORT=1234 some-command` sets a
variable for that command only, in plain
bash — a reminder that the .envrc sets
defaults for a folder, not laws.
