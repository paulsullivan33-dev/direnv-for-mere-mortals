# Glossary

**.envrc** — the file a folder carries to
declare its environment; a bash script
direnv runs on entry and reverses on
exit (chapters 1 and 4).

**Allow** — the recorded approval of one
.envrc at one path with one exact
content, granted by `direnv allow`; the
gate that stands between a folder and
your shell (chapter 5).

**Allow store** — direnv's data folder,
`~/.local/share/direnv/allow/`, holding
one hash-named file per approval
(chapter 2).

**Block** — both the state of an unapproved
file ("is blocked") and the command
(`direnv block`) that revokes an approval
(chapter 5).

**Diff** — the set of environment changes
direnv computes for a folder and prints
as shell text for the hook to evaluate;
its reverse, kept as `DIRENV_DIFF`, is
what makes a clean unload possible
(chapter 2).

**dotenv** — the stdlib helper that loads
a plain `.env` file of `NAME=value` lines
from inside an .envrc; also the name of
the format and of the loader family in
other tools (chapters 6 and 10).

**Hook** — the one line in a shell's
startup file that installs direnv's
prompt-time check (chapters 2 and 3).

**Layout** — a stdlib helper family that
prepares a language's whole working
environment inside the project —
`layout python3` builds and activates a
virtual environment (chapter 7).

**Load / unload** — applying an .envrc's
changes on entering its folder, and
reversing exactly those changes on
leaving (chapter 4).

**PATH_add** — the stdlib helper that
prepends a folder, named relative to the
.envrc, to the `PATH` for the duration of
the load (chapters 4 and 6).

**Prompt check** — the hook's habit of
re-examining the world before every
prompt, which is how direnv notices
entries, exits and edits without watching
anything (chapter 2).

**source_up** — the stdlib helper that
runs the nearest parent folder's .envrc
first, so a child folder inherits and
then overrides (chapter 6).

**Stdlib** — the standard library of
helper functions defined for every
.envrc; printed in full by
`direnv stdlib` (chapter 6).

**watch_file** — the stdlib helper that
adds a file an .envrc depends on to
direnv's watched set, so editing it
re-presents the environment (chapter 6).

**Whitelist** — a configuration entry
naming folder prefixes whose .envrc
files load without per-file approval;
a deliberate widening of the gate
(chapters 3 and 5).
