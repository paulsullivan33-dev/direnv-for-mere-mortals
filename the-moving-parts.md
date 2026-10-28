# The Moving Parts

Five parts, each with one job. Knowing
which part holds what turns every
surprise later in the guide into a named
problem.

## The binary

`direnv` itself is a single program (the
lab's is version 2.38.2) that does the
checking, the loading and the bookkeeping
— but almost never talks to you directly
while you work. You meet it through its
subcommands: `direnv allow`, `direnv
block`, `direnv status`, `direnv exec`,
and a handful more. Its full list is one
command away:

```bash
direnv --help
```

## The hook

Your shell is where the automation
happens, and direnv gets there with one
line in the shell's startup file —
chapter 3 installs it:

```bash
eval "$(direnv hook bash)"
```

The hook defines a small function the
shell runs **before every prompt**. That
placement is the whole trick: direnv does
not watch your folders or intercept `cd`.
Each time your shell is about to show a
prompt, the hook asks direnv "has the
environment for this folder been applied,
and is it still the approved one?" and
applies or reverses changes as needed.
This is why the blocked message in
chapter 4 appears the moment you arrive
in a folder, and why an edit to an .envrc
is noticed at the very next prompt
(chapter 5): the check runs constantly,
not just when you move.

The lab's hook, printed by `direnv hook
bash` (transcript
`direnv-lab/transcripts/t00-version-and-hook.txt`),
does exactly this: it calls `direnv
export bash`, receives a small shell
script describing the changes to make,
and evaluates it. Which brings us to —

## The diff

direnv never edits your shell's memory
directly; it cannot — it is a separate
program. Instead it works out the
**difference** between the environment as
it is and the environment as it should
be, and prints that difference as shell
commands (`export X=…`, `unset Y`, a new
`PATH`) for the hook to evaluate. The
announcement you see on loading —

```text
direnv: export +DATABASE_URL +FIELDKIT_ENV +FIELDKIT_PORT ~PATH
```

— is that diff in shorthand: `+` for
variables added, `~` for variables
changed. Before applying anything, direnv
also stores the *reverse* diff inside
your shell (in a variable named
`DIRENV_DIFF`, alongside `DIRENV_DIR`,
`DIRENV_FILE` and `DIRENV_WATCHES`, which
name what is loaded and what is being
watched for changes). Leaving the folder
means evaluating the reverse: what was
added is removed, what was changed is
restored to its old value, character for
character. This is why direnv can promise
a clean unload — it never has to guess
what your environment used to be; it kept
the receipt.

One consequence worth remembering for
chapter 12: the receipt covers only
direnv's own changes. If *you* change a
variable by hand while a project is
loaded, your change is yours — direnv's
unload still restores the value from
before the project loaded.

## The allow store

Approvals live outside your projects, in
direnv's own data folder:

```text
~/.local/share/direnv/allow/
```

Each approval is one file in that folder,
named by a long hash. The hash is
computed from the .envrc's **path and
contents together**, which is why both
moving a project and editing a single
character of its .envrc produce the same
result: no matching approval, file
blocked. Chapter 5 shows the store from
the lab (transcript
`direnv-lab/transcripts/t04-trust-block-and-allow-store.txt`):
two hash-named files for the lab's two
approved projects at that moment, nothing
else — the store is small enough to audit
by eye, and `direnv prune` clears out
entries for files that no longer exist.

## The standard library

Every .envrc runs with a library of
helper functions already defined — the
**stdlib**. `export` itself is plain
bash, but `PATH_add bin`, `dotenv`,
`source_up` and the `layout` family are
direnv's, written for exactly the jobs
.envrc files do. You can read the whole
library, with comments, whenever you
like:

```bash
direnv stdlib
```

(transcript
`direnv-lab/transcripts/t05-source-up-and-dotenv.txt`
shows its first lines.) Chapter 6 tours
the parts of it the lab exercised for
real; the library also carries helpers
for situations this guide only describes,
and reading it is the fastest way to
learn what an .envrc you are reviewing
*might* be doing — chapter 5's advice in
mechanical form.

## The status report

When the parts disagree with your
expectations, `direnv status` is the
X-ray: where the binary and config live,
which file is loaded, whether the file
direnv found is approved, and the path of
the approval itself. From the lab, mid
re-block (transcript
`direnv-lab/transcripts/t03-edit-reblocks-and-status.txt`):

```text
Loaded RC path ~/projects/fieldkit/.envrc
Loaded RC allowed 1
Loaded RC allowPath ~/.local/share/direnv/allow/bc6f7909…
Found RC path ~/projects/fieldkit/.envrc
Found RC allowed 1
```

Loaded versus Found — what your shell is
running versus what the folder on disk
now declares — is the distinction most
direnv confusion reduces to, and this
command is where the guide will send you
in chapter 12.
