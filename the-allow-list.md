# The Allow List

Outcome: a clear picture of what an
approval is, what voids it, how to revoke
one, and how the gate behaves against the
one input it exists for — a project from
a stranger. This is the chapter the rest
of the guide keeps pointing at; direnv
used carelessly is worse than no direnv,
because it turns "I looked at a folder"
into "code ran".

## What an approval covers

`direnv allow` records one fact: *this
user approved this file*. But "this file"
is defined strictly — by its **path** and
by a **hash of its contents** (chapter 2
showed the store: `~/.local/share/direnv/allow/`,
one hash-named file per approval). Both
halves matter:

- **Same file, new contents** → no match
  → blocked. An approval is not trust in
  the project's author, present or future;
  it is trust in one exact text.
- **Same contents, new path** → no match
  → blocked. Move or rename the project
  and you approve it again, because direnv
  cannot know the move was yours.

This strictness is deliberate and it is
the feature. It means a tool that updates
a project for you — a `git pull` that
changes the .envrc, a generator that
rewrites it — can never silently change
what runs in your shell.

## The edit that re-blocks

The lab watched it happen mid-session
(transcript
`direnv-lab/transcripts/t03-edit-reblocks-and-status.txt`).
Inside fieldkit, loaded and working, one
word of the .envrc was changed — the port,
8080 to 9090, with `sed`:

```text
lab$ sed -i s/8080/9090/ .envrc
direnv: error ~/projects/fieldkit/.envrc is blocked. Run `direnv allow` to approve its content
lab$ echo "FIELDKIT_PORT is [$FIELDKIT_PORT]"
FIELDKIT_PORT is []
```

Two things happened at the very next
prompt, and both are the safe behaviour.
The new file was blocked: not one line of
the changed version ran. And the *old*
environment was unloaded: while the file
is in dispute, direnv applies none of it,
rather than leaving yesterday's values
standing as if nothing had happened. The
correct response is the chapter 4 ritual
in miniature — read the change
(`git diff` in a real project tells you
exactly who changed what), then approve:

```text
lab$ direnv allow
direnv: loading ~/projects/fieldkit/.envrc
lab$ echo "FIELDKIT_PORT is [$FIELDKIT_PORT]"
FIELDKIT_PORT is [9090]
```

`direnv status` during such a dispute
shows the machinery's side of the story —
Loaded versus Found, and the approval
path — which chapter 2 introduced and
chapter 12 uses.

## Revoking: direnv block

Approval is yours to withdraw. Inside a
project, `direnv block` (its help lists
the aliases `deny`, `disallow`, `revoke`)
deletes the approval; at the next prompt
the environment unloads and the file is
blocked again (transcript
`direnv-lab/transcripts/t04-trust-block-and-allow-store.txt`):

```text
lab$ direnv block
lab$ echo "FIELDKIT_ENV is [$FIELDKIT_ENV]"
FIELDKIT_ENV is []
```

Reach for it when a project stops being
yours to trust — a collaborator leaves, a
repo changes hands, a tool you no longer
use has been rewriting the file — or
simply when you want the prompt to read
the file again with fresh eyes. `direnv
prune` is the housekeeping cousin: it
removes approvals and related records for
files that no longer exist, keeping the
store the size of your actual work.

## The stranger's project

Now the case the gate exists for. The lab
built a folder the way one arrives in
real life — in `~/Downloads`, from
somewhere else — whose `.envrc` contains
a line that downloads a script from the
internet and runs it, followed by an
innocent-looking export. (The address in
the lab's file is a reserved `.invalid`
name that cannot resolve to any real
server; the shape is what matters, and it
is a shape you will meet with a real
address on it.) Entering the folder
(transcript t04):

```text
lab$ cd ~/Downloads/mystery-project
direnv: error ~/Downloads/mystery-project/.envrc is blocked. Run `direnv allow` to approve its content
lab$ echo "MYSTERY is [$MYSTERY]"
MYSTERY is []
```

Blocked. Nothing ran — not the download,
not the export. And there the lab left
it: the folder was never allowed, and
was deleted unapproved. Study the two
failure modes this protects against,
because both are ordinary human moments,
not carelessness:

- **The helpful README.** "To set up,
  run `direnv allow`." Setup instructions
  are part of the delivery for the very
  code the gate is holding back. Reading
  the file takes twenty seconds; the file
  is short by design, which is precisely
  what makes "just read it" a fair rule.
- **The innocent change.** You allowed a
  project's .envrc last month. Today a
  routine update changes it, and the
  re-block feels like a nuisance standing
  between you and work. That nuisance is
  the system working at the only moment
  it matters — the moment the code
  changed.

If you cannot read an .envrc — it is long,
it loads other files you cannot see the
end of, it uses helpers you do not
recognise (read `direnv stdlib` for the
vocabulary, chapter 6) — the answer the
gate offers is the right one: don't
allow it, and work in that folder with
the environment set by hand until you can.

## Widening the gate on purpose

direnv can be told to trust a whole
folder tree without per-file approval:
a `whitelist` in `~/.config/direnv/direnv.toml`
(chapter 3), naming prefixes — your own
projects folder, say — under which .envrc
files load unasked. The lab did **not**
run one, and this paragraph is
documented-only. The honest trade: it
removes exactly the pause that this
chapter exists to defend, in exactly the
place new code arrives. For a folder that
only ever contains your own writing it
can be a sane convenience. For anything
that receives downloads, clones or other
people's work, it converts direnv from a
guarded convenience into an open door.
Most readers should leave the gate as it
is.
