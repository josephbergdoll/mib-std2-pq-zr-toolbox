# toolbox/

Support files that stay on the SD card and are used by `install.sh` and the
on-unit scripts. Marked `DO NOT TOUCH ANYTHING IN HERE` for end users; for
development it is editable, with the constraints below.

The existence of this folder is also how scripts find the card:
`util_checksd.sh` picks the first `/media/mp00*` volume containing `toolbox/`.
Do not rename it.

| Path | What it is | Notes |
| --- | --- | --- |
| `devesd/` | 60 stock developer GEM screens | See `devesd/README.md` |
| `gem/cpu/` | GEM 3.x edition of the SWDL payload | See `gem/README.md` |
| `scripts/util_update.sh` | Copy of the payload updater kept on the card | See below |
| `utils/dd`, `utils/in32` | Prebuilt QNX ARM ELF binaries | No source here; do not modify or replace |

## scripts/util_update.sh

A near-copy of `cpu/…/esd/scripts/util_update.sh`. The two currently differ: the
payload version also upgrades GEM 4.3 (`GEM.jar` of 242383 bytes) to 4.11, and
this copy does not. Which is intended is not recorded in the repo. If a task
changes update behaviour, ask whether both should change, and do not align them
unprompted.

This file is outside the `cpu/…/tsd` tree, so it has no `hashes.txt` entry.

## utils/

`dd` and `in32` are stripped ARM executables linked against `/usr/lib/ldqnx.so.2`.
They cannot run on a development machine. Treat them as opaque. `dd` is copied
to `/tmp` and used by `dump_emmc.sh`; no script or screen in the repo currently
references `in32`, so do not remove it on the assumption that it is unused
without asking.
