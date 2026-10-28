# Installing Direnv

Outcome: direnv installed, its hook alive
in your shell, and proof of both. The
install is two steps — the program, then
the hook — and the second is the one
people forget, producing the classic
symptom chapter 12 opens with: a
perfectly installed direnv that does
nothing at all.

## The program

direnv is distributed the usual ways: as
a package in most Linux distributions'
repositories (`apt install direnv`,
`dnf install direnv`, and their kin),
through Homebrew on macOS, and as a
single downloadable binary from the
project's GitHub releases — one file per
operating system and processor, needing
nothing else on the machine. This guide's
lab used the release binary for **2.38.2**
(the checksum-verified `direnv.linux-amd64`
from the project's releases page), placed
in `~/bin`, which was already on its
`PATH`. Any route ends at the same place:

Check (transcript
`direnv-lab/transcripts/t00-version-and-hook.txt`):

```text
lab$ direnv version
2.38.2
```

The version matters for one practical
reason: direnv's helpers and messages
evolve slowly but they do evolve, and
advice written for a much older release
may name things differently. This guide's
transcripts are all from 2.38.2.

## The hook

Installing the program changes nothing in
your shell until you add the hook. For
bash, open `~/.bashrc` and add, at the
**end** of the file:

```bash
eval "$(direnv hook bash)"
```

End matters: the hook should be the last
word, so nothing loaded after it can
overwrite the prompt machinery it
installs. Then start a new shell (or
`source ~/.bashrc`) so the line takes
effect. The lab's startup file, a scratch
one kept for the purpose, holds exactly
three lines — a `PATH` for its `~/bin`,
the hook, and a simple prompt — and
chapter 4's transcripts are that shell
behaving as yours now should.

What the line does, briefly (chapter 2
has the long version): `direnv hook bash`
prints a shell function; `eval` defines
it in your shell; bash then runs it
before every prompt. You can read what
you are installing before you install it,
which this guide recommends as a habit
far beyond direnv:

```bash
direnv hook bash
```

The lab's capture (t00) shows its head —
a function that preserves your previous
command's exit status, runs `direnv
export bash`, and evaluates the result.

Other shells have their own spelling of
the same line — `direnv hook zsh` into
`~/.zshrc`, and fish, tcsh, elvish and
PowerShell are also supported by the
`hook` command. The lab hooked **bash
only**; the other shells' hooks are
described here from the project's
documentation and are listed as
documented-only in `criteria.md`.

## Proving the hook is alive

Do not trust the install until the
mechanism has moved. The quickest proof
is chapter 4 in miniature, using any
folder you like:

```bash
mkdir -p ~/hooktest
printf 'export HOOKTEST=alive\n' > ~/hooktest/.envrc
cd ~/hooktest
```

You should be greeted by the blocked
message — `direnv: error … is blocked.
Run 'direnv allow' to approve its
content`. A refusal may feel like a
failure; here it is the success signal.
It proves the hook ran, found the file,
checked it against the allow store, and
refused to execute unapproved code. If
instead you get silence, the hook is not
in the shell you are using — chapter 12,
first entry. Approve your own test file
and watch it load:

```bash
direnv allow
echo $HOOKTEST    # alive
cd ~
echo "[$HOOKTEST]"  # [] — unloaded again
rm -rf ~/hooktest
```

## Where direnv keeps its things

Three places, all named by
`direnv status` (chapter 2): the program
itself wherever your install put it; your
approvals in `~/.local/share/direnv/`
(the allow store, plus a matching `deny`
area used by `direnv block`); and an
optional configuration folder,
`~/.config/direnv/`, which can hold a
`direnv.toml` for settings such as
whitelisting folders you trust wholesale.
The whitelist is real and useful for a
folder of your *own* projects; it is also
a deliberate widening of the gate,
chapter 5 discusses when that trade is
sane. Nothing in the basic install
creates it, and this guide's lab ran
without one.
