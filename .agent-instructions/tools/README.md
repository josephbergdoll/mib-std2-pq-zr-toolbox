# tools/

Host-side Python utilities for preparing files that go into `custom/`. This is
the only code in the repo that runs on a development machine. Nothing on the
unit uses it, and nothing here is listed in `hashes.txt`.

| Script | Purpose | Usage |
| --- | --- | --- |
| `extract-mcf.py` | Unpack images from a skin `images.mcf` | `extract-mcf.py <input> <outdir>` |
| `compress-mcf.py` | Repack images into an `.mcf` | `compress-mcf.py <original> <new> <imagesdir>` |
| `extract-startup_x.boot.py` | Unpack a boot-animation `.boot` container | `extract-startup_x.boot.py <input> <outdir>` |
| `compress-startup_x.boot.py` | Repack a `.boot` container | `compress-startup_x.boot.py <original> <new> <imagesdir>` |
| `generate_shadow.py` | Prompt for a password, print a DES-crypt shadow entry | interactive |
| `mib2image.py` | GIMP plug-in for the unit's image format | loaded by GIMP, not run directly |

## Before running anything

- `extract-mcf.py`, `compress-mcf.py` and `generate_shadow.py` run
  `pip install` for missing packages (`image`, `passlib`) at import time, into
  whatever interpreter launched them. Use a virtualenv, and tell the user before
  running them on their machine.
- They import `pkg_resources`, which needs `setuptools` and is absent from some
  current Python installs.
- `extract-startup_x.boot.py` uses `match`, so it needs Python 3.10 or newer.
- Several scripts end with `input("Press Enter to exit...")` and will block a
  non-interactive shell. `extract-mcf.py` and `extract-startup_x.boot.py` act on
  the current directory when given no arguments.
- `mib2image.py` is Python 2 and imports `gimp`; it only works inside GIMP's
  plug-in environment. Do not port it or run it standalone unless asked.
- There are no sample `.mcf` or `.boot` files in the repo and no tests. A change
  can only be verified against a file dumped from a real unit, so ask the user
  for one and round-trip it (extract, compress, compare with the original).

## Conventions

- Python 3, each script standalone with a comment header carrying authors and a
  changelog. Add a changelog line when behaviour changes.
- The compress scripts take the original container as a base and rewrite it;
  the binary layout is defined by the unit's firmware. Do not change struct
  formats, offsets or hash handling without a real file to test against.
- Keep the README section "How to use Python3 scripts from the Tools folder" in
  step with any change to arguments.
