# cpu/

The software-update (SWDL) payload. The unit reads `/metainfo2.txt` from the card
root, offers the release `MIBStd2_Online_Approval`, and copies
`cpu/onlineservices/1/default/` onto its filesystem root, so
`cpu/onlineservices/1/default/tsd/etc/persistence/esd/x` becomes
`/tsd/etc/persistence/esd/x` on the unit. `install.sh` does the same copy by hand
for console, telnet and QNX-VM installs.

```
cpu/
├── DO NOT touch anything in here          placeholder, keep
├── InfoFile/1/default/Update.txt          update name/version shown by SWDL
└── onlineservices/1/default/
    ├── hashes.txt                         size + SHA-1 of every payload file
    └── tsd/etc/persistence/esd/
        ├── mibstd2-*.esd                  22 menu screens
        └── scripts/*.sh                   117 ksh scripts
```

## metainfo2.txt (repo root)

- Signed (`[Signature]` block at the end). Any edit invalidates it. Never modify
  it, including whitespace and its CRLF line endings.
- Lists the supported hardware variants. Units not listed (see README) cannot
  install from SD; that cannot be fixed by editing the file.
- Pins `Update.txt` at 348 bytes with SHA-1 `7a005a0b…`, which matches the file
  in the repo. `Update.txt` must therefore stay byte-identical. The stray `B` in
  its `Bname.en_GB` line is part of those bytes.
- Its `cpu\onlineservices\1\default\Dir` entry (FileSize 11012, a checksum) does
  not match the current `hashes.txt` size or SHA-1. The repo does not say what
  that entry is computed over. Do not try to reconcile it.

## hashes.txt

One block per payload file, blocks separated by a blank line, CRLF line endings,
no trailing newline after the last block:

```
FileName = "/tsd/etc/persistence/esd/mibstd2-about.esd"
FileSize = "490"
Checksum = "2ba854c9e0741292f684129d842e2f4a15c572ab"
```

- `FileName` is the on-unit path, i.e. the path relative to
  `cpu/onlineservices/1/default`, with a leading `/`.
- `FileSize` is in bytes; `Checksum` is the SHA-1 of the raw bytes. A line-ending
  change alters both.
- Order is `.esd` files first, then `scripts/`, roughly alphabetical. Insert new
  entries next to their neighbours and leave existing order alone.
- Every file under `tsd/` must have exactly one entry, with no entries for files
  that do not exist.
- `toolbox/gem/cpu/onlineservices/1/default/hashes.txt` is a separate file: the
  same entries plus three for `/tsd/bin/system/{GEM.jar,SOP2,startup}` at the top.
  Update both.

The maintainers' generator (`hashes_calc.py`) is gitignored and absent. To check
both files after a change, run from the repo root:

```sh
python3 - <<'EOF'
import re, hashlib, os
for root in ["cpu/onlineservices/1/default", "toolbox/gem/cpu/onlineservices/1/default"]:
    text = open(root + "/hashes.txt", encoding="latin-1").read()
    entries = re.findall(r'FileName = "(.*?)"\s+FileSize = "(\d+)"\s+Checksum = "(\w+)"', text)
    listed, bad = set(), 0
    for name, size, sha in entries:
        listed.add(name)
        path = root + name
        data = open(path, "rb").read() if os.path.exists(path) else None
        if data is None or len(data) != int(size) or hashlib.sha1(data).hexdigest() != sha:
            bad += 1; print("BAD", root, name)
    on_disk = {os.path.join(d, f)[len(root):] for d, _, fs in os.walk(root + "/tsd") for f in fs}
    print(root, len(entries), "entries,", bad, "bad, unlisted:", sorted(on_disk - listed))
EOF
```

Expected today: 139 entries for `cpu/`, 142 for `toolbox/gem/cpu/`, 0 bad, nothing
unlisted. Also confirm the two `tsd/etc` trees match:
`diff -rq cpu/onlineservices/1/default/tsd/etc toolbox/gem/cpu/onlineservices/1/default/tsd/etc`.

## What can be done here

- Add, change or remove screens and scripts (see the two deeper notes), then
  mirror to `toolbox/gem/cpu/` and update both `hashes.txt`.

## What cannot

- Change `metainfo2.txt`, `Update.txt`, or the `cpu/onlineservices/1/default`
  directory layout. The paths are referenced by the signed metainfo, `install.sh`,
  `util_update.sh` and `util_restore.sh`.
