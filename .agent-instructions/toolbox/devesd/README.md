# toolbox/devesd/

Sixty stock VW/TechniSat developer screens for the Green Engineering Menu
(tuner, audio, Bluetooth, CarPlay, online services, debugging and so on). They
are vendor files, shipped as-is.

`copy_devesd.sh` ("Install extended Green Engineering Menu" on the GreenMenu
screen) backs up the unit's `/tsd/etc/persistence/esd`, copies this folder into
it, and deletes the reduced stock screens (`VW_Main_red.esd`,
`VW_Debugging_red_V02.esd`, `VW_Engineering_red_V04.esd`) when the full versions
are present. `restore_esd.sh` reverts to the backup.

## What can be done here

- Use these as reference for `.esd` syntax beyond what the toolbox screens use
  (`button`, `choice`, `slider`, `table`, `BIOSCtrl`).
- Add or remove a vendor screen when the user supplies or names the file.

## What to watch

- Do not edit the contents: no reformatting, no header changes, no line-ending
  normalisation. The numeric namespaces and keys address real persistence and
  adaptation values on the unit.
- File names matter. `copy_devesd.sh` tests for `VW_V01_Debugging.esd`,
  `VW_V01_Engineering.esd` and `VW_Main.esd` by name.
- These screens attach to stock parents (for example `screen Car Main`), not to
  the `MIBSTD2_Toolbox` tree. New toolbox screens belong in `cpu/…/esd/`, not
  here.
- Not covered by `hashes.txt`.
