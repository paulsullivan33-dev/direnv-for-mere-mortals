# Troubleshooting

The failures this tool actually produces,
in the order a new user meets them. Every
entry here was either captured in the
lab's transcripts or follows directly
from the mechanism chapter 2 describes.

## Nothing happens when I enter a folder

The classic. direnv is installed, the
.envrc is right there, and entering the
folder produces silence — no block, no
load, nothing. Almost always: **the hook
is not in this shell.** Check, in order:

1. Is the hook line present, at the end
   of the startup file your shell actually
   reads (`~/.bashrc` for interactive
   bash)? A line in `~/.bash_profile` or
   `~/.profile` may never be seen by the
   terminal you use.
2. Did this shell start *after* you added
   it? Open a new terminal, or
   `source ~/.bashrc`.
3. Is the shell interactive? The hook
   runs before a *prompt*; scripts and
   other non-interactive shells have no
   prompts and no direnv — which is by
   design, and chapter 8's `direnv exec`
   is the tool for them.

Test with the chapter 3 hooktest: a fresh
folder whose one-line .envrc should greet
you with a blocked message. Blocked is
the sound of success.

## The file blocks again after every edit

It is supposed to. An approval covers one
exact content (chapter 5); an edit — by
you, by a teammate's commit arriving in a
pull, by a formatter — makes the file a
new file as far as the gate is concerned.
Read the change and `direnv allow` again.
If the blocks arrive from a tool
*rewriting* the .envrc on a schedule, the
real fix is to stop the rewriting or move
the moving values into a file the .envrc
loads with `watch_file` discipline
(chapter 6), not to wish the gate away.

## "direnv: error .envrc file not found"

From the lab (transcript
`direnv-lab/transcripts/t90-traps.txt`):

```text
lab$ direnv allow
direnv: error .envrc file not found
```

typed at the home folder, where there is
no .envrc. `direnv allow` approves the
file found from *where you stand* — the
current folder or its parents. `cd` into
the project (or pass the file's path:
`direnv allow ~/projects/fieldkit`) and
approve again. The same applies to
`direnv block` and `direnv status`: they
speak about the folder you are in.

## I allowed it, and it still doesn't work — the syntax error

An approved file can still be a broken
program. The lab's broken project
(transcript t90) contains one line, an
unterminated command:

```text
lab$ cat .envrc
export BROKEN=(
lab$ direnv allow
direnv: loading ~/projects/broken/.envrc
./.envrc:1: unexpected EOF while looking for matching `)'
lab$ echo "BROKEN is [$BROKEN]"
BROKEN is []
```

direnv ran the file — your approval said
it could — bash reported the error in its
own words, and the load produced nothing.
All-or-nothing again: a partial load is
not left standing. The fix is in the
file, and a cheap check before allowing
any file you have edited is to read it as
bash would: `bash -n .envrc` parses
without running, and catches this class
of mistake before your shell does.

## "direnv … is taking a while to execute"

Seen in the lab's own layout transcript
(t06): direnv warns when an .envrc takes
more than a few seconds (the threshold is
the `warn_timeout` that `direnv status`
reports — 5s in the lab). It is a warning,
not an error: the load continues, and
`Ctrl-C` abandons it. First loads that
build things (chapter 7's virtual
environments, a tool compiling on first
use) trip it legitimately. An .envrc that
trips it *every* time is doing recurring
work at every prompt check that crosses
folders — network calls and package
installs are the usual suspects — and the
fix is to move that work behind a
condition ("only if the built thing is
missing") the way the layouts do, so the
per-entry cost falls back to checking.

## The layout says "python: command not found"

Transcript t06b, chapter 7: `layout
python` needs a `python` command; on a
machine offering only `python3`, the load
fails with direnv's "Could not find
python's version". Use `layout python3`.
The general shape of this failure — a
layout naming a command your machine
spells differently — is the same for
other languages: the stdlib source
(`direnv stdlib`) shows which command
each layout calls.

## A variable I changed by hand came back

You edited a loaded variable in the
shell; on leaving, direnv restored the
*pre-project* value, discarding your edit
along with the project's. That is the
receipt of chapter 2 working as designed:
the unload restores what was there before
the load, not what you later typed. If a
value must differ for one command, set it
for that command (`VAR=value command`,
chapter 8); if it must differ for the
session, change the .envrc and re-allow.

## Two hooks, twice the messages

A shell framework's direnv plugin *and*
a hand-added hook line (chapter 10) both
install the prompt function, and loads
report twice or race. Keep one route:
if your framework provides the plugin,
remove your line from the startup file,
or the reverse.

## When to read the status

`direnv status` (chapters 2 and 5) is the
first stop whenever the story above does
not match your symptom: it names the file
direnv *found*, the file your shell has
*loaded*, and whether each is approved —
and most mysteries are a disagreement
between those three lines.
