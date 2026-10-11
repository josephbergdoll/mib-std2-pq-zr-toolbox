# toolbox/gem/

The "special edition" payload for units on firmware 01xx/02xx with GEM 3.x, which
cannot run scripts. Users on those units replace the card's root `cpu/` folder
with `toolbox/gem/cpu/` before installing (README, install step 2).

`toolbox/gem/cpu/` is `cpu/` plus three files:

```
toolbox/gem/cpu/onlineservices/1/default/tsd/bin/system/
├── GEM.jar     GEM 4.11 (250770 bytes)
├── startup     replaces the unit's /tsd/bin/system/startup for one boot
└── SOP2        the normal startup script, restored by the step below
```

How the upgrade works: SWDL copies all three to `/tsd/bin/system/`. On the next
boot the modified `startup` moves `GEM.jar` to `/tsd/hmi/HMI/jar/GEM.jar`, moves
`SOP2` over itself (so later boots use the normal script), and makes the toolbox
scripts executable. `startup` and `SOP2` are identical except for that one
block.

## Rules

- `toolbox/gem/cpu/onlineservices/1/default/tsd/etc/` must be byte-identical to
  `cpu/onlineservices/1/default/tsd/etc/`. Edit in `cpu/` first, then copy.
  Check with
  `diff -rq cpu/onlineservices/1/default/tsd/etc toolbox/gem/cpu/onlineservices/1/default/tsd/etc`.
- `hashes.txt` here is its own file: the `cpu/` entries plus the three
  `/tsd/bin/system/` entries. Update it alongside the `cpu/` one.
- `InfoFile/1/default/Update.txt` is the same pinned file as in `cpu/`. Do not
  edit.
- `startup` and `SOP2` are the unit's boot script. An error in either can stop
  the unit booting. Do not change them without an explicit request, and keep
  the two in step apart from the upgrade block.
- `GEM.jar` is a binary with no source here. `install.sh` and `util_update.sh`
  copy it from this exact path (and `install.sh` also accepts `gem.jar`), so do
  not move or rename it.
