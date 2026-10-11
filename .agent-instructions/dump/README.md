# dump/

Empty in git apart from the placeholder `Dumps will be placed here`.

On a user's SD card, `dump_*` scripts copy files from the unit to
`dump/<TRAIN>/<SERIAL>/<SDPATH>/` through `util_dump.sh`. The intended workflow
(README, "How to use the toolbox to do customizations") is: dump, edit on a PC,
place the result in the matching `custom/` folder, then run the `copy_` script.

- Never commit dumps. They are large, per-unit, partly copyrighted firmware, and
  `dump_pvault.sh`, `dump_shadow.sh` and `dump_var.sh` output contains personal
  and security-relevant data.
- `dump/<TRAIN>/<SERIAL>/support` is what users zip and attach to an "unknown
  filesize" issue; it is the input needed to add a firmware to a `patch_*`
  script.
- `dump_emmc.sh` writes a full eMMC image (~7.3 GB) and needs an exFAT or NTFS
  card.
- Files dumped from a unit are suitable inputs for `tools/extract-*.py`.
