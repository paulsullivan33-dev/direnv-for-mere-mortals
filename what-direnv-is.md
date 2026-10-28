# What Direnv Is

Programs learn about the world they run
in partly from **environment variables**:
named values — `DATABASE_URL`, `PORT`,
`EDITOR`, the `PATH` that decides which
programs a command name finds — handed to
every program when it starts. Change the
variables and the same program behaves
differently: it connects to the test
database instead of the real one, listens
on another port, finds a different Python
first. Anyone who keeps more than one
project on a machine lives inside this
fact, and manages it, usually, by memory
and by hand: a sticky note of exports to
paste, a script to source and then
half-undo, a second terminal kept open
"in project mode" until the day the wrong
command runs in the wrong window.

**direnv removes the by-hand part.** It
is a small program that extends your
shell. A project folder carries a file
named **.envrc** describing the
environment that project wants. When you
`cd` into the folder, direnv runs the
file and applies what it declares; when
you leave — to anywhere outside the
folder — direnv reverses exactly those
changes. Variables it set are unset,
variables it changed are put back,
folders it added to your `PATH` are taken
off again. The environment becomes a
property of *where you are*, set up and
cleaned up by the same mechanism.

An .envrc is not a list of settings in
some configuration format. It is a
**bash script** — the same language your
shell speaks — with a library of helper
functions (chapter 6) for the common
jobs. That choice is direnv's strength
and its whole risk, and the next section
is about the risk, because the risk comes
first with this tool.

## The rule, stated before everything else

A file that runs automatically when you
enter a folder is a file an attacker
would love to write. So direnv's central
design decision is that **an .envrc never
runs on sight**. The first time you enter
a folder, direnv refuses, and prints:

```text
direnv: error ~/projects/fieldkit/.envrc is blocked. Run `direnv allow` to approve its content
```

Nothing has run. Not one line. The file
stays blocked until you — having read it —
type `direnv allow`, which records your
approval of *that file, with that exact
content*. Edit a single character and the
approval no longer matches: blocked
again, until you read the change and
approve again. Chapter 5 pulls this
mechanism apart, because using direnv
well mostly means using the gate well.

So the rule, and it is not fine print:

> **An .envrc is code. Only allow a file
> you have read and understood, from a
> source you trust — exactly as you would
> treat any script before running it.**

Your own projects, written by you: read
them once, allow them, done. A colleague's
project your team works on: read the
.envrc as part of joining, the way you
would read its install script. A
repository you cloned ten minutes ago
because a post recommended it: its .envrc
is unproven code from a stranger, and
"the setup instructions said to run
`direnv allow`" is the social engineering
that makes the attack work. The file can
contain anything bash can do — chapter
5's lab built a downloaded project whose
.envrc tried to fetch and run a script
from the internet, and the only thing
between that line and your machine was
the gate, plus a reader who looked first.
The lab never allowed it. Nothing ran.

## Why anyone would do this

- **One environment per project, with no
  ritual.** The test database address,
  the debug flag, the port: they arrive
  when you arrive and leave when you
  leave. No sourcing, no unsourcing, no
  wrong-window accidents (chapter 4).
- **Projects carry their own setup.** An
  .envrc is a file in the project, usually
  committed with it. A new machine, a new
  teammate, a fresh clone: the environment
  is declared in one readable place
  instead of living in somebody's memory.
- **Tools find their project versions.**
  An .envrc can put a project's own script
  folder on the `PATH`, point a tool at
  the project's config, or activate the
  project's Python environment (chapters
  6 and 7) — the setup a README would
  otherwise ask you to do by hand each
  morning.
- **Experiments stay in their folder.** A
  variable set to test something is set
  *there*, in that folder's file, and
  walking away ends the experiment —
  instead of leaking into every later
  command in the shell.

## A first taste

After installing (chapter 3), the whole
lifecycle fits on one screen. In a
project whose .envrc you have read:

```bash
cd ~/projects/fieldkit        # blocked the first time
direnv allow                  # approve this exact content
echo $FIELDKIT_PORT           # 8080 — loaded
cd ~                          # direnv: unloading
echo "[$FIELDKIT_PORT]"       # [] — gone again
```

The loading and unloading are announced
on the terminal as they happen —
`direnv: loading …`, `direnv: export
+FIELDKIT_ENV +FIELDKIT_PORT ~PATH`,
`direnv: unloading` — so the mechanism is
never invisible. The lab's full capture
of this sequence is transcript
`direnv-lab/transcripts/t02-allow-load-unload.txt`,
walked through in chapter 4.

## Who it is for

The developer with three projects that
want three different configurations. The
operator whose scripts assume a variable
that is set on one machine and not
another. The student following a course
whose setup steps begin "export these
five things" — for the eleventh time.
The common thread is one person, one
shell, and an environment that should
follow the folder instead of their
memory.

## What it is not

direnv is not a **secrets manager**: it
delivers variables to programs; it does
not store them safely, encrypt them, or
control who can read them (chapter 11).
It is not a **version manager**: it does
not install Pythons or Nodes — chapter 10
introduces the neighbour that does, and
the two are often used together. It is
not a general configuration system for
your whole login: an .envrc speaks for
one folder tree, and your shell's own
startup files remain the place for
settings that are truly yours everywhere.
And it only serves programs started from
a shell it has hooked — a program launched
from a desktop menu never passes through
your shell, and direnv never sees it
(chapter 11).
