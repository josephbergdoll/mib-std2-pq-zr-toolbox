# custom/skins/

What the toolbox can and cannot reskin, worked out from `copy_skins.sh`,
`restore_skins.sh`, `dump_skins.sh`, `patch_menumode.sh` and
`tools/*-mcf.py`. Skins live on the unit in `/tsd/hmi/Resources/skinN/`.

## What `copy_skins.sh` installs

For each `custom/skins/skinN/` folder on the card:

| File placed in `custom/skins/skinN/` | Target on unit | Contains |
| --- | --- | --- |
| `images.mcf` | `skinN/images.mcf` | All UI bitmaps |
| `ambienceColorMap.res` | `skinN/ambienceColorMap.res` | Accent / ambience colours (binary) |
| `info.txt` | `skinN/info.txt` | Skin settings (text) |
| `xx_XX.res` (e.g. `en_GB.res`) | `skinN/i18n/xx_XX.res` | UI text strings for one language |

- A file is copied only if it differs from the one on the unit (`cmp`), and the
  original is backed up first.
- Text resources go directly in `custom/skins/skinN/`, not in an `i18n/`
  subfolder. The script finds them recursively but copies from the top level.
- If `skinN` does not exist on the unit at all, the whole folder is copied as a
  new skin.
- `custom/skins/replace/skinN/` replaces an existing skin folder wholesale: the
  unit's folder is deleted, then the card's copied. It does nothing for a skin
  that is not already on the unit.

The placeholder folders are `skin0` to `skin8` plus `replace/`. Which skin a
unit actually uses is decided by the unit and is not recorded in this repo.

## Editing the files

- `images.mcf`: `tools/extract-mcf.py` writes `img_<n>.png`; with
  `imageidmap.res` beside it, it also sorts copies into named folders.
  `tools/compress-mcf.py` rebuilds the container from the same numbered PNGs.
  - Replacement is one-for-one. The count and numbering come from the original
    container; images cannot be added or removed.
  - PNGs must be mode `L` (8-bit greyscale) or `RGBA`. Anything else aborts.
  - The repacker writes whatever width and height the PNG has. Whether the HMI
    tolerates changed dimensions is not known; advise keeping the original size.
- `ambienceColorMap.res` and `xx_XX.res`: no tool in the repo reads or writes
  these formats.
- Boot animation is separate: `custom/startanim/` with
  `tools/*-startup_x.boot.py`.

## What skinning cannot do

- Change layout, add or remove screen elements, or change behaviour. That is in
  the HMI program (`/tsd/hmi/tsd.mibstd2.hmi.ifs`), which the toolbox can only
  replace wholesale with a user-supplied file (`copy_hmi.sh`).
- Fonts: nothing in the repo handles fonts, and where the unit keeps them is not
  documented here. Do not state a font path or format without a dump to check.
- Clock faces: only replaceable to the extent they are bitmaps in `images.mcf`.
  Not verified for any unit.

## Backup and restore pitfalls

Read these before telling a user that a skin change is reversible.

- `restore_skins.sh` restores only `images.mcf`, `ambienceColorMap.res` and
  `i18n/`. It does not restore `info.txt` or any other file, even though
  `copy_skins.sh` backs `info.txt` up.
- A whole-folder replace can skip its backup. File backups and folder backups
  share `backup/<train>/<serial>/skins/skinN/`, and the folder backup is skipped
  when that directory already exists. So if a single file of `skinN` was
  replaced earlier, a later `replace/skinN` deletes the unit's folder with only
  that one file backed up.
- `patch_menumode.sh` edits `info.txt` in every skin in place with no backup.
- Restore runs from the Green Engineering Menu, so it needs the unit to boot.
  A full eMMC dump (`dump_emmc.sh`) is the only backup independent of that.

When helping a user reskin, have them run `dump → skins` first and keep that
dump off the card as their own copy of the originals.
