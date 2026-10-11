# r32bitte

Notes on my own car's unit. Fork-only: this file must never reach the upstream
repo (`olli991/mib-std2-pq-zr-toolbox`). See "Fork-only files" in `AGENTS.md`.

## Head unit (MIB2 PQ, Discover Media)

- Part number: 5C0 035 684 D (HW 5C0 035 684 B)
- Component: MU-S-NS-US, hardware H31, software 0478
- Serial: VWZAZ2T1034929
- Dataset: V03935249AS 0001 (EV_MUStd4CTSAT 001022)
- Label file: 5G0-035-MIB-STD2
- Map: 3G0919866BB, version 0525, NAR 2017
- Toolbox: MIB STD2 PQ/ZR Toolbox v1.4.2 (GEM 4.11t)
- Supplier: Eurozone Tuning (Bill), Discover Media MIB2 PQ Gen 1 kit, 6-month warranty
- Processor: i.MX6. It was listed on the GEM hardware page (IMX6, 50°C).
- Manufacturer: TechniSat. The dataset name EV_MUStd4CTSAT has "TSAT" in it.
- Firmware: 0478, US PQ. The firmware train name is most likely MST2_US_VW_PQ_P0478T.

### Current 5F coding

```
02730002FF00000051220005001808009F0100D400A08105C6
```

- Byte 11 = 05: external BOOSTER (amp)
- Byte 19 = D4: rear camera low-line on, USB C0
- Byte 21 = A0: GPS antenna on
- Byte 22 = 81: AWD

Original EZT coding, for reference:

```
02730002FF00000051220001001808009F0100C400200105C6
```

## Gateway

- 7N0 907 530 BL (HW 7N0 907 530 AN, H56 1682), coding 350000
- Installation list: 01 02 03 04 08 09 15 16 17 19 22 25 42 44 46 47 52 55 5F 65
- Old gateway: 1K0 907 530 L, coding ED831F071003020000

## Connected to it

- Camera: rcd330.com flip badge, dynamic lines; grey wire to B6, black to B12
- Mic: EZT MIB2 mic, overhead console
- GPS puck under the dash
- FM: one white lead connected; dual FAKRA adapter (white double end, Z singles) pending
- USB/AUX harness; Carlinkit 5.0 for wireless CarPlay
- Sirius: not connected

### Faceplate buttons

- Left: BAND, MEDIA, PHONE, VOICE
- Right: EJECT, NAV, INFO, MENU
- No APP button.

## Toolbox notes for this unit

- Button remapping: the only `rb_*.sh` script with a US PQ 0478 entry is
  `rb_info2app.sh` (INFO>APP), keyed to a `configurationmanager.res` of 75208
  bytes, labelled `MST2_US_VW_PQ_P0478 cpu`. The faceplate has an INFO button,
  so this is the applicable remap. Not yet confirmed that this unit's file is
  that size or whether it is `cpu` or `cpuplus`; the script refuses to patch if
  the size differs.

## Open questions

- [ ] What does INFO open today? Note it here before remapping, since the
      function comes off the button.
- [ ] Does `INFO>APP` apply ("Patch is applied") or report "Unknown file size"?
      If unknown, record the size it prints.
- [ ] After the remap, does INFO go straight into CarPlay with the Carlinkit
      connected, and to the App-Connect screen without it? (Expected, unverified.)
- [ ] Is there already a CarPlay / App-Connect entry in the Media source list
      while a session is active?
- [ ] `cpu` or `cpuplus`? (`mib_info` screen.)
- [ ] Skins: run `dump → skins`. Which `skinN` is in use, are there font files
      under `/tsd/hmi/Resources`, and are there clock graphics in `images.mcf`?
      Keep a copy of that dump off the SD card before changing anything.
