# MIB STD2 PQ/ZR Toolbox — agent instructions

This repo is the contents of an SD card. Unzipped onto a FAT32 card and installed
through the head unit's Software Update screen (or `install.sh`), it injects extra
screens and ksh scripts into the Green Engineering Menu (GEM) of VW-group
MIB STD2 PQ/ZR Technisat/Preh infotainment units. `README.md` is the user manual.

Detailed, per-directory notes live in `.agent-instructions/`, laid out like the
repo itself. Read the note for a directory before changing anything in it:

| Working in | Read |
| --- | --- |
| `cpu/`, `metainfo2.txt`, `hashes.txt` | `.agent-instructions/cpu/README.md` |
| `cpu/.../esd/*.esd` (menu screens) | `.agent-instructions/cpu/onlineservices/1/default/tsd/etc/persistence/esd/README.md` |
| `cpu/.../esd/scripts/*.sh` | `.agent-instructions/cpu/onlineservices/1/default/tsd/etc/persistence/esd/scripts/README.md` |
| `custom/`, `dump/`, `backup/` | `.agent-instructions/custom/README.md` (and `dump/`, `backup/`) |
| Skins, `images.mcf`, reskinning questions | `.agent-instructions/custom/skins/README.md` |
| `toolbox/` | `.agent-instructions/toolbox/README.md` (plus `gem/`, `devesd/`) |
| `tools/` | `.agent-instructions/tools/README.md` |

## What you cannot do here

- **Nothing in the payload can be run or tested on this machine.** The scripts
  target QNX Neutrino 6.5 on ARM (i.MX6) and assume on-unit paths such as
  `/tsd/...` and `/media/mp000`. There is no test suite, linter or CI. Never
  report a script change as tested; say it was reviewed by reading only.
- **A wrong byte can brick a car's head unit.** Do not guess patch offsets, file
  sizes, firmware trains or on-unit paths. If a value is not in the repo or
  supplied by the user, ask for it.
- **`metainfo2.txt` is digitally signed and must never be edited**, reformatted
  or re-saved with different line endings. Same for `cpu/InfoFile/1/default/Update.txt`,
  whose size and SHA-1 are pinned inside it.
- The release packaging scripts (`make_package.sh`, `make_package.bat`,
  `hashes_calc.py`) are gitignored and not in the repo. Do not assume how the
  release zip is built.

## Rules that apply to every change

1. **Mirror the payload.** `toolbox/gem/cpu/` is a second copy of `cpu/` for units
   with GEM 3.x. Every `.esd` and `.sh` under `cpu/onlineservices/1/default/tsd/`
   must be byte-identical in `toolbox/gem/cpu/onlineservices/1/default/tsd/`.
2. **Update both `hashes.txt` files** whenever a payload file is added, removed or
   changed by even one byte. Format and a verification snippet are in
   `.agent-instructions/cpu/README.md`.
3. **Preserve line endings byte for byte.** There is no `.gitattributes`. Shell
   scripts are LF (CRLF breaks ksh on the unit); `.esd`, `hashes.txt`,
   `metainfo2.txt` and `Update.txt` are mostly CRLF, with a few `.esd` files LF
   or mixed. Keep whatever each file already uses and do not normalise.
4. **Scripts are ksh for QNX**, not bash. No bashisms, no GNU-only flags, and only
   utilities the existing scripts already use.
5. Files with names like `Place custom sounds here` or `DO NOT touch anything in
   here` are intentional placeholders that keep empty directories in git and on
   the SD card. Leave them.

## Adding a feature (checklist)

1. Write the script in `cpu/.../esd/scripts/` following the conventions in that
   directory's note.
2. Add a `script` entry to the right `mibstd2-*.esd` screen.
3. If users supply a file, add a `custom/<topic>/` folder with a placeholder file.
4. Copy the new and changed files into `toolbox/gem/cpu/...`.
5. Update both `hashes.txt` files.
6. Update the menu overview or FAQ in `README.md` if user-visible behaviour changed.
7. For a release: bump the version in `mibstd2-main.esd` (header and welcome
   label) and add a line at the top of `mibstd2-history.esd`.

## Commits and pull requests

This is a fork. `origin` is `josephbergdoll/mib-std2-pq-zr-toolbox`; `upstream`
is `olli991/mib-std2-pq-zr-toolbox`. These rules apply to work in the fork.

- **Commit messages and PR titles use [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/):**
  `<type>(<optional scope>): <description>`, imperative mood, lower-case
  description, no trailing period, subject at most 72 characters.
  - Types: `feat`, `fix`, `docs`, `refactor`, `chore`, `build`, `ci`, `revert`.
  - Suggested scopes: `scripts`, `esd`, `hashes`, `gem`, `devesd`, `custom`,
    `tools`, `install`, `readme`, `agents`.
  - Breaking changes: `!` after the type or scope, plus a `BREAKING CHANGE:`
    footer.
  - Examples: `feat(scripts): add voice-to-app remap for MST2_EU_VW_PQ_P0480T`,
    `fix(hashes): update checksums for copy_poi.sh`,
    `docs(agents): add per-directory instructions`.
- Upstream history does not follow this convention. Leave existing commits as
  they are; do not rewrite history to convert them.
- **PR bodies follow `.github/pull_request_template.md`.** Keep every heading and
  write "N/A" under any that does not apply. `gh pr create` does not pre-fill
  the template when `--body` is passed, so copy the headings in by hand.
- Under "Testing", there are no automated tests in this repo, and payload
  changes cannot be run locally. Say exactly what was and was not verified
  (for example "hashes re-verified; not run on a unit").
- **Open PRs against the fork, not upstream.** `gh` defaults to the parent repo
  for forks, so pass `--repo josephbergdoll/mib-std2-pq-zr-toolbox`. Only target
  `olli991` when explicitly asked.

### Fork-only files must never reach upstream

`r32bitte.md` (the owner's personal unit notes) is tracked in this fork and must
never appear in `olli991/mib-std2-pq-zr-toolbox`, in a commit, a PR diff, a PR
or issue body, or a comment. The same goes for the other fork-only files:
`AGENTS.md`, `CLAUDE.md`, `.agent-instructions/` and
`.github/pull_request_template.md`.

- Never push to the `upstream` remote. Its push URL is disabled in the owner's
  clone; do not re-enable it.
- The fork's `master` contains these files, so a branch cut from it carries them
  into any upstream PR. If an upstream PR is explicitly requested, create the
  branch from `upstream/master` and cherry-pick only the relevant commits.
- Before opening an upstream PR, run
  `git diff --name-only upstream/master...HEAD` and stop if any fork-only file
  is listed.
- Do not quote the contents of `r32bitte.md` (serial number, coding, part
  numbers) anywhere outside this fork.

## Known oddities (do not silently "fix")

- `copy_poi.sh` is the only CRLF shell script; its hash in `hashes.txt` matches
  the CRLF bytes.
- `toolbox/scripts/util_update.sh` differs from the payload `util_update.sh`
  (it lacks the GEM 4.3 → 4.11 upgrade block).
- `rb_voice2nav.sh` prints "Remapping VOICE to CAR button..." although it remaps
  VOICE to NAV. Only the message is wrong.
- The `cpu\onlineservices\1\default\Dir` size and checksum in `metainfo2.txt` do
  not equal the size or SHA-1 of the current `hashes.txt`. What that entry covers
  is not documented in this repo.

Raise these with the user if a task touches them.
