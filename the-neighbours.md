# The Neighbours

direnv sits in a crowded neighbourhood of
tools that manage environments, versions
and project setup. These are
characterisations, not benchmarks — each
is the right tool for someone, and the
point of the chapter is the boundaries.

## Mise — the version manager next door

**Mise** (this series has a guide to it)
installs and pins *tool versions* per
project: this folder's Node, that
folder's Python, recorded in a file the
project commits. It also manages some
environment variables of its own, which
makes the boundary worth stating plainly:
mise answers "which Python runs here?",
direnv answers "what is in the
environment here?". Many people use mise
alone for simple projects, direnv alone
where versions are settled, or both —
mise pinning the tools, an .envrc shaping
everything else, with the .envrc free to
call the versions mise installed. If your
pain is versions, start there; if your
pain is configuration, it is this guide's
tool.

## Plain .env loaders

A large family — `dotenv` libraries in
every language, `dotenv-cli`, the env-file
support built into Docker Compose and
many frameworks — loads `NAME=value` files
into *one program's* environment at the
moment that program starts. The
differences from direnv are scope and
direction: a loader serves a single
process (and its children) when *it*
chooses to read the file, while direnv
serves your whole shell session by
folder, and unloads on exit. They compose
happily — chapter 6's `dotenv` helper is
exactly the bridge — and for a single
service with a single config file, the
loader alone is often the smaller tool.

## Language environment activators

Python's `venv` asks you to
`source .venv/bin/activate` and later
`deactivate`; Node's version managers,
Ruby's, and others have their own
rituals. They do one language's job
thoroughly, and the ritual is the cost:
it is manual, per-shell, and easy to
forget in both directions. A direnv
`layout` (chapter 7) is best understood as
those same rituals, automated and given
an exit behaviour — the layout sets the
same `VIRTUAL_ENV` the activator would.
If you work in one language and like the
ritual's explicitness, nothing here is
broken; direnv earns its place at the
second project or the second language.

## Shell frameworks and prompt tools

Oh My Zsh, bash-it and their kin organise
your shell's own configuration —
aliases, prompts, completions, plugins,
sometimes including a direnv plugin that
is simply the chapter 3 hook wearing the
framework's colours. They configure the
shell *as yours, everywhere*; direnv
configures it *per folder*. Complements,
not rivals — but note that a framework's
plugin and a hand-added hook should not
both be installed, or the prompt gets two
cooks (chapter 12).

## The hand-rolled script

The ancestor of them all: a `setup.sh` or
`env.sh` in each project that you source
by hand. It has one advantage — nothing to
install — and the two flaws direnv exists
to remove: it relies on you remembering,
in both directions (source on arrival,
and what exactly did it change that I
must now undo?), and it runs unexamined
because *you* are the mechanism. If a
project's whole environment is two
variables and you are its only user, the
script is honestly fine. The moment a
second person, a second project, or a
subfolder arrives, the folder-following
tool starts paying for itself.
