# Recipes

Short, complete answers to the situations
that send people to direnv. Each names
the file to write; all of them were
exercised in the lab in the form the
earlier chapters show, unless a recipe
says otherwise.

## A different port and database per project

The founding recipe (chapter 4). In each
project's .envrc:

```bash
export APP_PORT=8080
export DATABASE_URL=postgres://localhost/myapp_dev
```

Two projects can now both claim "the"
port and "the" database address in their
own files without colliding in your
shell, because only one project's values
are ever loaded at a time — the one whose
folder you are standing in.

## The project's own scripts, on the path

```bash
PATH_add bin
```

Chapter 4's move. Scripts the project
keeps in its `bin/` become commands while
you work on the project and stop existing
when you leave. Name project scripts
distinctively — the `PATH` you prepend to
is shared with the whole system, and a
project script called `test` or `ls`
would shadow the real ones inside that
folder. (Direnv will happily let you;
shadowing is a decision, so make it a
deliberate one.)

## Shared defaults, one override

A parent folder holds the team-wide
defaults; a subproject changes one value
(chapter 6, transcript t05):

```bash
# in the child's .envrc
source_up
export APP_ENV=experiment
```

Every variable the parent declares
arrives; the child's lines, running after
`source_up`, win where they speak.
Remember the double approval: parent and
child files are each allowed separately.

## An old .env file keeps working

The project already has a `.env` its
other tools read (chapter 6):

```bash
# the whole .envrc
dotenv
```

One line bridges the formats. Keep the
values in the `.env` where the other
tools expect them, and let direnv read
the same file rather than maintaining
two lists that will drift apart.

## A Python project in one line

```bash
layout python3
```

A virtual environment in `.direnv/`,
activated on entry, gone on exit, and a
per-project `pip install` that touches
nothing else (chapter 7). Add `.direnv/`
to the project's `.gitignore` in the same
commit.

## Secrets stay out of the committed file

The .envrc is usually committed — it is
documentation as much as configuration.
API keys and passwords are neither. The
pattern that respects both facts:

```bash
# .envrc — committed
export APP_ENV=production
dotenv .env.local
```

with the secrets in `.env.local`, listed
in `.gitignore`, never committed.
Teammates create their own from a
committed `.env.local.example` that names
the variables without values. Understand
what this pattern does and does not do:
it keeps secrets out of the *repository*.
On the machine they still sit in a plain
file and land in your shell's
environment, readable by anything running
as you — which is why chapter 11 insists
direnv is a delivery mechanism, not a
safe, and why an allowed .envrc must
itself be trusted code.

## A folder that asks for nothing

Not every folder needs an .envrc, and an
empty one is a small smell: it loads
nothing, but its approval still guards a
file future edits could fill. Leave
folders that need no environment without
one, and let the absence say so.
