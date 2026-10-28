# Direnv for Mere Mortals

Every project on your machine wants its
own little world: this one needs a
database address and port 8080, that one
needs a different Python and a folder of
its own scripts on the path, a third
falls apart if a particular variable is
missing. The usual ways of providing that
world are all bad in the same way — you
provide it by hand, every time, and
forget on the days it matters. **direnv**
is a small program that hooks into your
shell and does it automatically: enter a
folder and the environment that folder
declares is loaded; leave, and every
change is undone, as if you had never
been there.

The declarations live in a file called
**.envrc** in the project's folder. It is
not a settings file in the usual sense —
it is a small **bash script**, run by
your shell, which is what makes direnv
flexible, and also what makes it
dangerous in exactly one well-understood
way. So direnv has a gate: an .envrc
**never runs until you have approved
that exact file, with that exact
content**, by typing `direnv allow`.
Change one character and it is blocked
again until you approve the new version.
That gate is the heart of this guide, and
chapter 5 is devoted to it.

The rule that governs everything here is
stated in chapter 1 and repeated in
chapter 5: **an .envrc is code someone
wrote. Read it before you allow it, the
way you would read any script before
running it — and treat a project you have
just downloaded or cloned as untrusted
until you have.** A hostile .envrc in a
stranger's repository is not a
hypothetical: it is the one attack this
tool makes possible, and the allow gate
is the defence, provided you actually
read what you approve. The lab obeyed
the rule strictly, including building a
fake hostile project of its own to prove
the gate holds: the file stayed blocked
and nothing in it ever ran.

The lab behind this guide ran the real
thing: **direnv 2.38.2**, the official
release binary, hooked into a real
interactive bash running in a terminal,
with a scratch home folder so nothing
touched a real account. Against sample
projects written for the guide it proved
the whole lifecycle: the blocked message
on first entry, `direnv allow`, the
variables arriving and being used, the
project's own script folder joining the
path, everything unloaded on exit, an
edit re-blocking the file mid-session,
`direnv block` revoking an approval, a
child folder inheriting its parent's
environment with `source_up`, a plain
`.env` file loaded with `dotenv`, and a
Python **layout** that built a real
virtual environment, installed a real
package into it, and was left behind on
exit. `direnv exec` ran a project command
with the project's environment while the
shell outside stayed untouched.

What the lab could not do, it did not
fake. Only bash was hooked and exercised;
the hooks for zsh, fish and the other
shells direnv supports are described from
the project's documentation. Editor
integrations and per-machine whitelists
are likewise taught, not demonstrated,
and `criteria.md` keeps the full ledger
of what was run and what was only read.

## What this guide covers

- [**What direnv is**](what-direnv-is.md) —
  the mental model: an environment per
  folder, loaded on entry and unloaded on
  exit, and the security rule that is
  part of the tool, not an afterthought
- [**The moving parts**](the-moving-parts.md) —
  the shell hook, the prompt-time check,
  the difference direnv applies and later
  reverses, the allow store, and the
  standard library every .envrc can call
- [**Installing direnv**](installing-direnv.md) —
  the release binary and the packages,
  the one hook line in your shell's
  startup file, and how to prove the hook
  is alive
- [**Your first .envrc**](your-first-envrc.md) —
  a sample project end to end: blocked on
  arrival, approved once read, variables
  in use, and the clean unload on exit
- [**The allow list**](the-allow-list.md) —
  the trust system taken seriously: what
  an approval covers, why an edit revokes
  it, how to revoke one yourself, and the
  downloaded project you must never
  approve unread
- [**Writing .envrc files**](writing-envrc-files.md) —
  the standard library: `export`,
  `PATH_add`, `dotenv`, `source_env` and
  `source_up`, `watch_file`, and reading
  the library's own source with
  `direnv stdlib`
- [**Layouts**](layouts.md) — one line
  that builds a project's Python home: a
  virtual environment created, used and
  left behind, and the layouts for other
  languages
- [**Beyond the prompt**](beyond-the-prompt.md) —
  `direnv exec` for one command with a
  project's environment, exporting an
  environment for scripts and services,
  and the editor integrations
- [**Recipes**](recipes.md) — a port and
  database per project, project scripts
  on the path, shared defaults with
  per-folder overrides, a legacy `.env`
  kept working, secrets kept out of the
  committed file
- [**The neighbours**](the-neighbours.md) —
  Mise, plain `.env` loaders, virtual
  environment activators and shell
  frameworks: what each does that direnv
  doesn't, and vice versa
- [**Limits**](limits.md) — an .envrc is
  arbitrary code, only hooked shells are
  served, desktop programs never see it,
  and direnv is not a safe for secrets
- [**Troubleshooting**](troubleshooting.md) —
  the silent shell, the file that blocks
  again after every edit, the allow typed
  in the wrong folder, the syntax error,
  and the traps this guide's lab fell
  into so you don't have to
- [**Glossary**](glossary.md) — the
  terms, collected in one place

## What it doesn't cover

Storing secrets safely — direnv delivers
variables to your shell, it does not
protect them, and chapter 11 explains why
an approved .envrc must be treated as
trusted code rather than as a container.
Installing and version-managing
languages and tools is a neighbouring
job (chapter 10 points at the tool this
series uses for it); direnv arranges the
environment those tools run in.
Organisation-wide configuration —
forcing approvals, managing whitelists
for a fleet — is pointed at, not taught;
this guide stays with one person and
their own machine, where the tool does
its honest work.

## How to use this guide

Read chapter 1 for the model and the
rule, and chapter 2 before installing
anything — knowing which part holds what
turns later confusion into a named
problem. Chapter 4 takes you through the
full lifecycle in one sitting, and
chapter 5 is not the optional chapter:
understand the allow list before you
approve anything you did not write
yourself. The lab's sample projects live
in `direnv-lab/fixtures/` and its
transcripts in `direnv-lab/transcripts/`;
its distilled commands are
`examples/commands.txt` and its notes
`examples/lab-notes.txt`.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Bash for Mere Mortals](https://paulsullivan33-dev.github.io/bash-for-mere-mortals/)
- [Mise for Mere Mortals](https://paulsullivan33-dev.github.io/mise-for-mere-mortals/)
- [Linux for Mere Mortals](https://paulsullivan33-dev.github.io/linux-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/direnv-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/direnv-for-mere-mortals/blob/main/LICENSE-CODE).
