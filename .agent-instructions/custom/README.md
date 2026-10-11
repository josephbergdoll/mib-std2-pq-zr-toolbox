# custom/

Drop zone on the SD card. Users put modified files here; `copy_*` scripts push
them to the unit. In git every folder holds only a placeholder file named after
what belongs there (`Place custom ringtones here`), plus two shipped jars in
`custom/java/`. Never commit real user files, firmware binaries or dumps here.

| Folder | Expected content | Script | Target on unit |
| --- | --- | --- | --- |
| `carplayicon/` | `oemIcon.png` | `copy_carplayicon.sh` | `/tsd/etc/sal/carplay/oemIcon.png` |
| `fec/` | `signed_exception_list.txt` | `copy_fec.sh` | `/tsd/etc/slist/signed_exception_list.txt` |
| `greenmenu/` | `*.esd`, and scripts in `greenmenu/scripts/` | `copy_esd.sh` | `/tsd/etc/persistence/esd` |
| `hmi/` | `tsd.mibstd2.hmi.ifs` | `copy_hmi.sh` | `/tsd/hmi/tsd.mibstd2.hmi.ifs` |
| `java/` | `navignore_audi.jar`, `navignore_vw.jar` (shipped) | `activate_navignore.sh` | see script |
| `mapcfg/` | mapcfg files | `copy_mapcfg.sh` | `/tsd/var/nav/cfg/mapcfg` |
| `mirrorlink/` | `mirrorlink.config.common.xml` | `copy_mirrorlink.sh` | `/tsd/etc/mirrorlink/mirrorlink.config.common.xml` |
| `navigation/` | `mibstd2_nav_target.ini` | `copy_nav.sh` | `/tsd/etc/nav/mibstd2_nav_target.ini` |
| `onlineservices/` | online services files | `copy_onlineservices.sh` | `/tsd/var/onlineservices` |
| `ringtones/` | `*.wav` | `copy_ringtones.sh` | `/tsd/etc/waveplayer/ringtones` |
| `shadow/` | `shadow` file (from `tools/generate_shadow.py`) | `privacy_shadow.sh` | `/etc/shadow` |
| `skins/skinN/` | `images.mcf`, `ambienceColorMap.res` | `copy_skins.sh` | `/tsd/hmi/Resources/skinN/` |
| `skins/replace/` | whole skin folders | `copy_skins.sh` | `/tsd/hmi/Resources/` |
| `sounds/` | `*.wav` | `copy_sounds.sh` | `/tsd/etc/waveplayer/sound` |
| `startanim/` | `startup_x.boot` | `copy_startanim.sh` | `/tsd/etc/startanim` |
| `stationdb/` | `VW_STL_DB.sqlite` | `copy_stationdb.sh` | `/tsd/etc/stationdb/VW_STL_DB.sqlite` |
| `swap/` | patched `tsd.mibstd2.system.swap` | `copy_swap.sh` | `/tsd/bin/swap/tsd.mibstd2.system.swap` |
| `swdl/` | patched `tsd.mibstd2.system.swdownload` | `copy_swdl.sh` | `/tsd/bin/swdownload/tsd.mibstd2.system.swdownload` |
| `tts/` | wave files | `copy_tts.sh` | `/tsd/etc/speech/tts/tones/Tones22kHz16Bitmono` |

The script column is the source of truth; re-check the script if this table and
the code disagree, then fix the table.

## What can be done here

- Add a folder for a new customisation: create `custom/<topic>/` with a
  placeholder file whose name tells the user what to put there, then write the
  matching `copy_`/`dump_`/`restore_` scripts with `TOPIC=<topic>`.
- Rename a placeholder to make its instruction clearer.

## What to watch

- Folder names are the `SDPATH`/`TOPIC` values in the scripts. Renaming a folder
  means changing every script that uses it, in both payload copies, plus hashes.
- Nothing in `custom/` is listed in `hashes.txt`; that file covers only the
  `cpu/onlineservices/1/default/tsd` tree.
- The two jars are binaries with no source in this repo. Do not regenerate or
  edit them.
