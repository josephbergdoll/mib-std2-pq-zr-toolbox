# cpu/…/tsd/etc/persistence/esd/

Green Engineering Menu screen definitions (`.esd`). The GEM loads every `.esd` in
`/tsd/etc/persistence/esd/` on the unit. All toolbox screens are named
`mibstd2-*.esd`; the installer, updater and uninstaller delete by that glob, so
keep the prefix.

## Screen tree

The first non-comment line is `screen <Name> <Parent>`. A screen appears as an
entry inside its parent, so navigation is defined by these lines alone.

```
Main                                  (stock GEM root)
└── MIBSTD2_Toolbox                   mibstd2-main.esd
    ├── About                         mibstd2-about.esd
    │   ├── Disclaimer                mibstd2-disclaimer.esd
    │   └── History                   mibstd2-history.esd
    ├── Customization                 mibstd2-customization.esd
    │   ├── Adaptation                mibstd2-adaptation.esd
    │   │   ├── Car_BAP_Device_List   mibstd2-carBAPdeviceList.esd
    │   │   ├── Car_CAN_Device_List   mibstd2-carCANdeviceList.esd
    │   │   ├── Car_Device_BUS        mibstd2-carDeviceBUS.esd
    │   │   └── Car_Menu_Operations   mibstd2-carMenuOp.esd
    │   ├── Advanced                  mibstd2-advanced.esd
    │   ├── GreenMenu                 mibstd2-greenmenu.esd
    │   ├── Mirror-Link               mibstd2-mirrorlink.esd
    │   ├── Navi                      mibstd2-nav.esd
    │   ├── Remap_Buttons             mibstd2-remapbuttons.esd
    │   ├── Skins                     mibstd2-skins.esd
    │   └── Sounds                    mibstd2-sounds.esd
    ├── Dump                          mibstd2-dump.esd
    ├── MIB_Info                      mibstd2-mibinfo.esd
    ├── Network                       mibstd2-network.esd
    ├── Tools                         mibstd2-tools.esd
    └── Update_and_Uninstall          mibstd2-uninstall.esd
```

Screen names contain no spaces (use `_`). Renaming a screen breaks every child
whose `screen` line names it as parent.

## Elements used

`#` starts a comment. Elements are separated by blank lines; attributes are
indented with tabs or spaces (both occur, match the file you are in).

Static text line:

```
keyValue
    value   String sys 0x00000000 0
    label   "Reboot after installing new files."
    poll    0
```

Run a script (output is shown in the GEM console pane):

```
script
   value    sys 1 0x0100 "/tsd/etc/persistence/esd/scripts/copy_ringtones.sh"
   label    "Copy new ringtones from /custom/ringtones to unit"
```

Also present, mostly in the adaptation screens and `toolbox/devesd/`: `button`,
`choice`, `slider` (with `limits`), `BIOSCtrl`, and `table` (with `content`,
`columns`, `maxrows`, `orientation`, `entry`). These read and write persistence
or adaptation values by numeric namespace and key (`per 0x01a000d7 199 1`).
Copy an existing element of the same kind and change only what you understand;
the numeric IDs are not documented in this repo and must not be invented.

## Conventions

- Keep the comment header (author, version, breadcrumb) at the top of each file.
- Script paths are absolute on-unit paths under `/tsd/etc/persistence/esd/scripts/`.
  Every referenced script must exist in `scripts/` (currently all do).
- Labels are one line each. Longer text is split over consecutive `keyValue`
  elements, as in the existing files; keep new labels no wider than those.
- An empty `label ""` keyValue is the spacer between groups.
- Line endings vary per file (most CRLF, a few LF or mixed). Preserve them.
- The toolbox version lives in `mibstd2-main.esd` (header `Version:` and the
  `Welcome to … v1.5` label). `mibstd2-history.esd` is the changelog, newest
  entry first, one `keyValue` per version.

## After any edit

Copy the file to the same path under `toolbox/gem/cpu/` and update both
`hashes.txt` (see `.agent-instructions/cpu/README.md`). If the menu structure
changed, update the menu overview in the root `README.md`.
