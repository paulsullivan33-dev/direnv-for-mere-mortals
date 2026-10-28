# Your First .envrc

Outcome: one full lifecycle, watched
closely — a project entered, refused,
read, approved, used, and left clean.
Every later chapter is a footnote to
something on this page.

The lab's sample project is **fieldkit**,
a small invented tool with the usual
needs: an environment name, a port, a
database address, and a folder of its own
scripts. Its fixtures ship with this
guide in `direnv-lab/fixtures/fieldkit/`,
so you can rebuild the project exactly.
Its `.envrc` is four lines:

```bash
export FIELDKIT_ENV=development
export FIELDKIT_PORT=8080
export DATABASE_URL=postgres://localhost/fieldkit_dev
PATH_add bin
```

Three plain exports of the kind a README
would ask you to paste by hand, and one
stdlib helper (chapter 6): `PATH_add bin`
puts the project's own `bin` folder —
which holds a `fieldkit-hello` script —
at the front of the `PATH`, but only
while you are inside the project.

## Arrival: the refusal

Enter the folder for the first time
(transcript
`direnv-lab/transcripts/t01-first-contact-blocked.txt`):

```text
lab$ cd ~/projects/fieldkit
direnv: error ~/projects/fieldkit/.envrc is blocked. Run `direnv allow` to approve its content
lab$ echo "FIELDKIT_ENV is [$FIELDKIT_ENV]"
FIELDKIT_ENV is []
```

Read that error as the tool working, not
failing. direnv found the file, checked
its path and contents against the allow
store, found no approval, and ran none of
it — the variable is empty because the
script that would set it was never
executed. This pause is the whole
security model (chapters 1 and 5): there
is no mode in which a new or changed
.envrc runs first and asks later.

So the next step is not a command. It is
reading:

```bash
cat .envrc
```

Four lines, all of them things you would
happily type yourself. *Now* the command
earns its place.

## Approval and loading

```text
lab$ direnv allow
direnv: loading ~/projects/fieldkit/.envrc
direnv: export +DATABASE_URL +FIELDKIT_ENV +FIELDKIT_PORT ~PATH
```

(from transcript
`direnv-lab/transcripts/t02-allow-load-unload.txt`).
`direnv allow` records the approval — the
file's path and a hash of its contents,
into the allow store of chapter 2 — and
the hook, checking at that same prompt,
loads the environment immediately. The
two `direnv:` lines are the loading in
public: which file ran, and the diff it
applied. `+` marks variables that were
added; `~PATH` marks the `PATH` changed
(because `PATH_add` rewrote it).

Now the environment is simply *there*,
for every command in this shell:

```text
lab$ echo "FIELDKIT_PORT is [$FIELDKIT_PORT]"
FIELDKIT_PORT is [8080]
lab$ fieldkit-hello
fieldkit running in development mode on port 8080
database: postgres://localhost/fieldkit_dev
```

Note the second result: `fieldkit-hello`
is not installed anywhere on the system.
It is the project's own script, found
because `bin` is on the `PATH` — the
.envrc arranged it, and no other folder
on this machine can run that command.

## Leaving: the unload

```text
lab$ cd ~
direnv: unloading
lab$ echo "FIELDKIT_ENV is [$FIELDKIT_ENV]"
FIELDKIT_ENV is []
lab$ fieldkit-hello
bash: fieldkit-hello: command not found
```

The unload is as total as the load.
direnv evaluates the reverse diff it kept
(chapter 2): the three variables are
removed, the `PATH` returns to exactly
what it was — which is why the script is
suddenly "command not found" again, the
surest sign nothing leaked. Walk back
into the project and it reloads without
asking: the approval is on record, the
contents unchanged, so there is nothing
new to approve. That asymmetry is the
daily experience of the tool — friction
exactly once per file version, silence
thereafter — and it holds until the file
changes, which is where chapter 5 begins.

## Two habits to form now

**Read before you allow, every time the
file is new to you** — including when it
changes under you in a project you share
(chapter 5 shows an edit re-blocking a
file mid-session, old values unloaded
until you approve the new ones). And
**let projects declare themselves**:
when a project needs a variable, put it
in the .envrc next to the code that
needs it, not in your shell's startup
file, where it will follow you into
every other project you own.
