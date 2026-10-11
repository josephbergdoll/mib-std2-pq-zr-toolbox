# cpu/…/esd/scripts/

ksh scripts that run as root on the head unit (QNX 6.5, ARM) when a `script`
entry in a GEM screen is pressed. They are installed to
`/tsd/etc/persistence/esd/scripts/` and reference each other by that absolute
path. stdout is what the user sees on screen, so every step is `echo`ed.

None of this can be executed locally. Review by reading; never claim a run.

## Families

| Prefix | Purpose |
| --- | --- |
| `copy_*` | Install a user file from `custom/<topic>/` on the card to the unit |
| `dump_*` | Copy files from the unit to `dump/<train>/<serial>/…` on the card |
| `restore_*` | Put back the copy saved in `backup/<train>/<serial>/…` |
| `patch_*` | Binary-patch a system file in place (SWDL, SWAP, CP, nav, mirrorlink, menu mode) |
| `rb_*` | Remap a hardware button by patching `configurationmanager.res`; `restore_buttons.sh` undoes it |
| `activate_*` / `deactivate_*` | Toggle telnet, console, qconn, permanent access, navignore, auto-install |
| `privacy_*` | Delete logs, caches, phone data, SWDL history; reset shadow |
| `update_*`, `delete_*` | Maps and POI handling |
| `util_*` | Shared helpers, sourced by the others (plus update, uninstall, reboot, sysinfo entry points) |
| other | `custom_script.sh` (runs `toolbox.sh` from the card), `move_screenshots.sh`, `save_sysinfo.sh`, `test_internet.sh` |

## Shared helpers and their contract

Callers `export` these, then source helpers with `. /tsd/…/scripts/util_x.sh`:

| Variable | Meaning |
| --- | --- |
| `TOPIC` | Feature name; also the backup subfolder and usually the `custom/` subfolder |
| `MIBPATH` | Absolute target path on the unit |
| `SDPATH` | Path relative to `custom/`, `dump/<train>/<serial>/` or `backup/<train>/<serial>/` |
| `TYPE` | `"file"` or `"folder"` |

| Helper | Does |
| --- | --- |
| `util_checksd.sh` | Sets `VOLUME` to the first `/media/mp00*` containing a `toolbox/` folder; exits 1 if none |
| `util_info.sh` | Sets `SYS`, `TRAIN`, `MUVER`, `SERIAL` from the persistence vault and `sloginfo` |
| `util_backup.sh` | Copies `MIBPATH` to `$VOLUME/backup/$TRAIN/$SERIAL/$TOPIC`, only if no backup exists yet |
| `util_copy.sh` | Copies `$VOLUME/custom/$SDPATH` to `MIBPATH`; remounts rw unless the target is under `/tsd/var` |
| `util_restore.sh` | Copies the backup to `MIBPATH`, remounts ro, and exits |
| `util_dump.sh` | Copies `MIBPATH` to `$VOLUME/dump/$TRAIN/$SERIAL/$SDPATH` and exits |
| `util_mount.sh` / `util_mount_ro.sh` | Remount the system partition (`/dev/hd0t177`) rw / ro |

Things that follow from the helpers and are easy to break:

- The backup is taken once and never overwritten, so it stays the original
  factory file. Do not add code that refreshes or deletes backups.
- `util_copy.sh` and `util_restore.sh` hard-code which `TOPIC`s get `chmod 777`
  (`hmi`, `mirrorlink`, `navigation`, `swap`, `swdl`, `shadow`). A new executable
  or security-relevant target needs adding to both lists.
- `util_copy.sh` reads from `toolbox/` instead of `custom/` when `TOPIC=devesd`.
- Helpers are sourced, so an `exit` inside one ends the calling script.

## Shape of a typical script

```ksh
#!/bin/ksh
export TOPIC=ringtones
export MIBPATH=/tsd/etc/waveplayer/ringtones
export SDPATH=$TOPIC
export TYPE="folder"

echo "This script will install custom ringtones"
echo

. /tsd/etc/persistence/esd/scripts/util_backup.sh     # back up first
. /tsd/etc/persistence/esd/scripts/util_copy.sh       # remounts rw, copies
. /tsd/etc/persistence/esd/scripts/util_mount_ro.sh   # always return to ro

echo
echo "Done. Please restart the unit."
exit 0
```

Order matters: back up, write, remount read-only, tell the user whether to
reboot or reopen the GEM, `exit 0`.

## Binary patch scripts (`patch_*`, `rb_*`)

These identify the firmware by the **byte size** of the target file and patch
fixed offsets:

```ksh
set -A bytes 07 07 07 07                 # replacement bytes, one per offset
case $size in
    1541236) #mainstd EU ZR 253/254      # comment names the train(s)
        set -A offsets 6EE80 72328 72A4C BE660 ;;
esac
```

- Supporting another firmware means adding a `case` arm with its size, offsets
  (hex, no `0x`) and a comment naming the train. Sizes and offsets come from
  analysing that firmware's binary (users attach it via the "unknown filesize"
  issue template). They cannot be derived or guessed from other arms.
- The number of offsets must not exceed the number of entries in `bytes`.
- Unknown sizes must keep falling through to the `Unknown file size` message
  without writing anything.
- The patched copy is built in `/tmp`, then moved over the original only after a
  backup. Keep that order.
- README FAQ links point at these files as the list of supported firmware, so
  keep the train comments accurate.

## Button remaps on offer (`rb_*`)

All patch `/tsd/hmi/HMI/res/configurationmanager.res`; `restore_buttons.sh`
restores it from backup. A reboot applies the change.

| Script | Menu label | Firmware arms |
| --- | --- | --- |
| `rb_setup2app.sh` | SETUP>APP | 3 |
| `rb_traffic2app.sh` | TRAFFIC>APP | 11 |
| `rb_info2app.sh` | INFO>APP | 6 |
| `rb_tp2app.sh` | TP>APP | 2 |
| `rb_mute2app.sh` | MUTE>APP | 5 |
| `rb_setup2nav.sh` | SETUP>NAV | 2 |
| `rb_menu2nav.sh` | MENU>NAV | 1 |
| `rb_voice2car.sh` | VOICE>CAR | 5 |
| `rb_voice2app.sh` | VOICE>APP | 2 |
| `rb_voice2nav.sh` | VOICE>NAV | 4 |
| `rb_voice2mute.sh` | VOICE>MUTE | 1 |

- Only these pairings exist. Each is two hard-coded bytes written at two
  per-firmware offsets; there is no general any-button-to-any-function path.
- To answer "does remap X work on firmware Y", grep the script's `case` arms.
  The match is on the file's byte size, so the train named in the comment is a
  label, not the test. An unknown size changes nothing.
- The repo does not define what the targets (APP, NAV, CAR, MUTE) or sources
  open. Do not present button behaviour as fact from the repo.
- Targets are existing hardkey functions only. Soft items such as entries in
  the Media source list are not remappable this way.
- The counts above go stale; re-count from the scripts before quoting them.

## Style

- `#!/bin/ksh` (QNX pdksh). Arrays via `set -A`, hex via `$((16#$i))`. No bash
  features, no GNU long options.
- Tabs for indentation in most files; match the file you are editing.
- LF line endings. `copy_poi.sh` is CRLF in the repo, which is an existing
  oddity, not a pattern to copy.
- Unit paths and device names (`/dev/hd0t177`, `/media/mp00*`, `/net/J5`) are
  facts about the hardware. Do not change them without a source.

## After any edit

Copy the file to the same path under `toolbox/gem/cpu/`, update both
`hashes.txt`, and add or adjust the `script` entry in the relevant `.esd`.
Scripts not referenced from any `.esd` today: the `util_*` helpers listed above,
`util_dump_root.sh`, `copy_mirrorlink.sh`, `copy_nav.sh` and `restore_fec.sh`.
