# Writing .envrc Files

Outcome: an .envrc that does the common
jobs — variables, paths, inherited
defaults, a legacy `.env` — using the
standard library instead of hand-rolled
shell, and the knowledge of where the
full library lives.

An .envrc is bash, so anything bash can
do is permitted (chapter 11 takes the
dark side of that sentence seriously).
But most files need only a handful of
moves, and direnv's **standard library** —
the stdlib — provides them as functions
that handle the unload bookkeeping for
you. Print it any time with `direnv
stdlib` (its first lines are in
transcript
`direnv-lab/transcripts/t05-source-up-and-dotenv.txt`);
it is a commented shell script, and
reading it is the reference manual.

## export — the plain move

```bash
export FIELDKIT_PORT=8080
```

`export` is bash's own. It earns its place
here for one direnv-specific reason: when
the file is unloaded, exported variables
are removed or restored automatically,
because the hook keeps the reverse diff
(chapter 2). An .envrc made of nothing but
exports is the common case, and there is
no shame in it.

## PATH_add — paths that clean up

```bash
PATH_add bin
```

`PATH_add` prepends a folder to `PATH`,
resolved **relative to the .envrc's own
folder** — so `bin` means the project's
`bin` wherever the project lives, on any
machine. Writing `export PATH=$PWD/bin:$PATH`
by hand works too, but `PATH_add` is the
spelling that survives the project moving
and that every direnv reader recognises.
Chapter 4 showed the result: the
project's `fieldkit-hello` runnable
inside the project, unfindable outside
it. One more from the same family, worth
knowing by name: `PATH_add` is built on a
sibling, `path_add`, that takes a variable
name first — the spelling for path-like
variables other than `PATH` itself (and
`path_rm` removes an entry again). All
three are in the stdlib's source.

## source_up and source_env — inheritance

Projects nest. The lab's fieldkit has an
`experiments` subfolder that wants
everything the parent project declares,
with one value changed. Its entire
.envrc (fixtures ship with the guide):

```bash
source_up
export FIELDKIT_ENV=experiment
```

`source_up` finds the nearest .envrc in a
parent folder and runs it first; the
child's own lines then override what they
name. The load shows both files running
(transcript t05):

```text
lab$ direnv allow
direnv: loading ~/projects/fieldkit/experiments/.envrc
direnv: loading ~/projects/fieldkit/.envrc
lab$ echo "FIELDKIT_ENV is [$FIELDKIT_ENV]"
FIELDKIT_ENV is [experiment]
lab$ echo "FIELDKIT_PORT is [$FIELDKIT_PORT]"
FIELDKIT_PORT is [8080]
```

The override won where it spoke (the
environment name); everything else was
inherited (the port, the database, the
path). Two cautions. Each .envrc keeps
its own approval: the child *and* the
parent must each be allowed — the lab's
transcript shows the child blocked until
its own `direnv allow`, parent already
approved or not. And `source_up` is a
trust decision in both directions: the
child runs the parent's code, so allowing
the child means its parent's file is part
of what you approved reading.

`source_env <folder>` is the sideways
version: run the .envrc of a *named*
folder rather than a parent — the way to
share one common environment file across
sibling projects that agree to share it.

## dotenv — the legacy .env

Plenty of tools already speak the plain
`.env` format — lines of `NAME=value`,
no shell, no helpers. direnv meets them
halfway. The lab's fieldkit-legacy keeps
its values in a `.env`:

```text
FIELDKIT_ENV=staging
FIELDKIT_PORT=8081
```

and its .envrc is one word:

```bash
dotenv
```

The result (transcript t05) is the values
loaded exactly as if exported in the
.envrc, and unloaded on exit the same
way. Note what the approval covered: the
.envrc containing `dotenv` — and be aware
that loading *through* a helper means
your approval extends to the file the
helper reads, so a changed `.env` deserves
the same glance as a changed .envrc.
(direnv can also approve a bare `.env`
file directly, without an .envrc at all —
the `allow` command's own help names
".envrc or .env file"; the lab used the
.envrc route, and the direct route is
documented-only here.)

## watch_file — approvals that notice more

An approval's hash covers the .envrc
itself. If your .envrc pulls in other
files — with `source_env`, `dotenv`, or
plain bash `source` — `watch_file
<path>` inside the .envrc adds those
files to direnv's watched set, so editing
them reloads (and, for loading purposes,
re-presents) the environment instead of
leaving stale values standing. The lab
did not need it — `dotenv` and `source_up`
register their own files — but hand-rolled
`source` lines are where it earns its
place.

## Saying something: log_status

An .envrc that needs to tell you something
(usually on a slow or conditional load)
should use `log_status "message"` rather
than `echo`: it prints in direnv's own
`direnv:` style, and only when direnv is
actually reporting. Its counterpart
`log_error` reports and fails the load.
Both are in the stdlib; the lab's files
were silent ones, so these two are
described from the library's own comments
rather than from a transcript.

## The rest of the shelf

The stdlib also carries `use` helpers
that adapt direnv to other tools —
version-manager bridges such as
`use node` and `use rbenv`, the Nix and
Guix integrations (`use nix`, `use guix`,
`use flake`), an `rvm` helper — and the
`layout` family of the next chapter.
These are real but were not run in this
guide's lab; they are listed as
documented-only in `criteria.md`, and the
stdlib source on your own machine is the
honest place to meet them.
