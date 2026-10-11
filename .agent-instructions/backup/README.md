# backup/

Empty in git apart from the placeholder `Backups will be placed here`.

On a user's SD card, `util_backup.sh` writes the original copy of anything the
toolbox is about to overwrite to `backup/<TRAIN>/<SERIAL>/<TOPIC>/`, once per
topic. `restore_*` scripts read it back through `util_restore.sh`.

- Never commit anything else into this folder. Contents are per-unit and may
  include firmware binaries and the unit's shadow file.
- The `<TRAIN>/<SERIAL>/<TOPIC>` layout is shared by `util_backup.sh` and
  `util_restore.sh`. Change both together or existing users' backups stop being
  found.
- If asked to work on a real card's contents: this folder is the only way back
  to factory files. Do not delete, rename or overwrite anything in it.
