# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's traffic is the lab talking to itself.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 11 names what the tool
   cannot do and must not be asked to do: an .envrc is arbitrary
   code that approval does not make safe, only hooked shells
   are served so desktop-launched programs never see the
   environment, direnv stores and protects no secrets, and
   the environment itself is a blunt, global instrument.
8. **The ethics rule is in the text, early.** Chapter 1 and
   the README state the security rule plainly — *an .envrc is
   code you run; read it before you allow it, and treat any
   project you have just downloaded or cloned as untrusted
   until you have* — before any procedure depends on it, and
   chapter 5 returns to it with the gate's mechanics, the
   re-block on edit, and a lab-built hostile .envrc (a
   download-and-run line on a reserved, unresolvable address)
   shown staying blocked and never allowed. The threat is
   named exactly: cloning a repository with a hostile .envrc
   is safe only because the gate holds and the reader reads.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a 2-CPU, 7.7 GB Ubuntu 24.04 sandbox.
direnv **2.38.2** was installed from the project's official
GitHub release binary (`direnv.linux-amd64`, SHA-256 recorded
in the lab notes) into a scratch home folder, and hooked into
a **real interactive bash running in a terminal (a pty)** — the
prompt-time behaviour in every transcript is the shell's own,
not a simulation. All transcripts live in
`direnv-lab/transcripts/`; the sample projects are
`direnv-lab/fixtures/`.

Run for real, with transcripts as evidence:

- Version and the bash hook's own text (`direnv hook bash`),
  installed as the last line of the scratch home's `.bashrc`
  (t00).
- The full lifecycle on the fieldkit fixture: blocked on first
  entry with variables provably empty; `direnv allow`; the
  loading and export lines; variables in use; the project's
  script found through `PATH_add bin`; exit, unloading, and
  the script unfindable again (t01, t02).
- An .envrc edited mid-session (`sed`, one word): the instant
  re-block at the next prompt **and** the unload of the old
  values, `direnv status` in full during the dispute, and
  re-approval bringing the new value live (t03).
- The hostile downloaded project: blocked on entry, its
  contents read, nothing in it run, never allowed; the allow
  store listed (one hash file per approval); `direnv block`
  revoking a working project and `direnv allow` restoring it
  (t04).
- The stdlib exercised: `source_up` inheritance with a child
  override (both files' loading lines visible; the override
  wins, the rest is inherited), `dotenv` loading a plain
  `.env`, and `direnv stdlib` itself (t05).
- `layout python3` end to end: the virtual environment
  created in `.direnv/python-3.12`, `VIRTUAL_ENV` set, a real
  package (`cowsay` 6.1) installed from the public index and
  run, and the unload on exit — including direnv's genuine
  "taking a while to execute" warning on first build (t06).
- The `layout python` failure on a machine with only
  `python3`: the load refused, nothing half-built (t06b).
- `direnv exec` running a project's command with the project's
  environment from outside it, the calling shell provably
  untouched (t07).
- The traps, run on purpose: `direnv allow` in a folder with
  no .envrc ("error .envrc file not found"), and an approved
  but syntactically broken .envrc producing bash's own error
  and loading nothing (t90).

Documented only — described from the project's documentation,
the command help and the stdlib's own commented source, not
run, and the text does not claim otherwise:

- The hooks for zsh, fish, tcsh, elvish and PowerShell; only
  bash was hooked and exercised.
- Editor and IDE integrations (chapter 8), and `direnv
  export`'s non-bash dialects (json, systemd, GitHub
  Actions format) beyond the bash export the hook itself
  evaluates.
- The `direnv.toml` whitelist that approves whole folder
  trees (chapter 5 discusses its trade honestly; the lab ran
  with the default gate).
- Approving a bare `.env` file directly, without an .envrc
  (named in `direnv allow`'s help; the lab used the `dotenv`
  route).
- The stdlib's `use` helpers, its non-Python layouts, and
  `log_status` / `log_error` / `watch_file` (read in the
  stdlib source; the lab's files did not call them).
- direnv on macOS and in Windows Unix-like environments.

Fixtures are this guide's own and deliberately generic: three
small invented projects (a field-data tool, its legacy twin,
its Python twin), a deliberately broken one-line file, and the
hostile download of chapter 5, whose payload address is a
reserved `.invalid` name that cannot resolve to a real server.
No example is modelled on any real person, network, service or
organisation, and no code the lab did not write itself was ever
approved or run.
