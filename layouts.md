# Layouts

Outcome: a project that carries its own
Python — created by one line, used while
you are inside, left behind when you go —
and an honest map of the layouts the lab
did not run.

A **layout** is the stdlib's answer to a
job bigger than setting variables:
preparing a whole working environment for
a language, in the way that language
expects, inside the project's own folder.
The job every Python project shares is
the **virtual environment** — a private
Python with its own package folder, so
one project's libraries never collide
with another's. Doing it by hand is a
three-step ritual (create it, activate
it, remember to deactivate). A layout
folds it into the .envrc.

## layout python3, run for real

The lab's third sample project,
fieldkit-py, has a one-line .envrc:

```bash
layout python3
```

First entry, the by-now-familiar refusal;
then approval, and the load does real
work (transcript
`direnv-lab/transcripts/t06-layout-python3.txt`):

```text
lab$ direnv allow
direnv: loading ~/projects/fieldkit-py/.envrc
direnv: export +VIRTUAL_ENV ~PATH
lab$ which python
~/projects/fieldkit-py/.direnv/python-3.12/bin/python
lab$ echo "VIRTUAL_ENV is [$VIRTUAL_ENV]"
VIRTUAL_ENV is [~/projects/fieldkit-py/.direnv/python-3.12]
```

What the one line did:

- **Created a virtual environment** where
  none existed, at
  `.direnv/python-3.12/` inside the
  project — direnv's state folder for the
  project, named for the Python it holds.
- **Set `VIRTUAL_ENV`** to it and put its
  `bin` at the front of the `PATH`, which
  is exactly what activating a venv does
  by hand — so `python` and `pip` now
  resolve inside the project.
- **Kept the receipt** like any other
  load: leaving the folder unloads
  `VIRTUAL_ENV` and restores the `PATH`,
  and the project's Python simply stops
  being the one your shell finds.

And it is a working Python, not a label.
In the same session the lab installed a
real package from the public package
index into that environment and ran it:

```text
lab$ python -m pip install cowsay
Successfully installed cowsay-6.1
lab$ python -c "import cowsay; cowsay.cow('direnv works')"
  ____________
| direnv works |
```

(the full ASCII cow is in the transcript;
she has been cropped here for width). The
package landed in the project's private
environment — the system Python never
saw it — and on the next entry the layout
finds its environment already built and
reuses it: creation happens once, not at
every prompt.

One honest detail from the same
transcript: the first load took long
enough that direnv said so —
`direnv: … is taking a while to execute.
Use CTRL-C to give up.` Building a venv
is real work, and later loads are fast.
Chapter 12 returns to that warning,
which direnv shows whenever an .envrc
takes more than a few seconds.

## python or python3 — a spelling that matters

On the lab's machine only `python3`
exists; there is no `python` command.
The natural first draft of the .envrc,
`layout python`, failed honestly
(transcript
`direnv-lab/transcripts/t06b-layout-python-missing.txt`):

```text
direnv: loading ~/projects/fieldkit-py/.envrc
environment:909: python: command not found
direnv: Could not find python's version
```

`layout python` wants a `python` of a
specific version family; `layout python3`
is the spelling for "the machine's
Python 3". Nothing loaded, nothing
half-built — another of direnv's
all-or-nothing habits. If your .envrc
must also work on machines where the
command differs, that is a real problem
real teams solve in the file (a
conditional on what exists); for one
person and their machines, picking the
spelling your machines speak is enough.

## The .direnv folder

The layout's working state lives in the
project, in `.direnv/`. Treat it as
**machine state, not source**: it is
rebuilt by the layout when missing, it
can be deleted to force a clean rebuild
(delete the folder, re-enter, the layout
recreates it), and in a version-controlled
project it belongs in `.gitignore`
alongside the virtual environments it
replaces. Your teammates' machines will
grow their own.

## The other layouts

The stdlib ships layouts for a shelf of
languages and tools — in the lab's
version, `go`, `julia`, `node`, `perl`,
`php` and `ruby`, plus a Python family
(`python`, `python3`, `pyenv`, `pipenv`,
`anaconda`, `uv`) — and the Nix and Guix
worlds are served by the `use` helpers of
chapter 6 rather than by layouts.
They share the pattern this chapter
proved with Python: prepare the
environment inside the project, expose it
through the same variables the tool
itself would set, unload on exit. The
lab ran **only the Python layout**; the
others are documented-only in this guide,
described from the stdlib's own source —
read yours with `direnv stdlib` and
search for `layout_`, where each is a
short commented function rather than a
promise.
