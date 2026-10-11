# .agent-instructions

Notes for coding agents, one `README.md` per repo directory that needs
explaining. The folder tree mirrors the repo: the note for `tools/` is
`.agent-instructions/tools/README.md`, and so on. Root-level rules are in
`/AGENTS.md`; `/CLAUDE.md` only imports that file.

```
.agent-instructions/
├── README.md                     this file
├── backup/README.md              on-card backups written by the unit
├── cpu/README.md                 SWDL payload, metainfo2.txt, hashes.txt
│   └── onlineservices/1/default/tsd/etc/persistence/esd/
│       ├── README.md             Green Engineering Menu screens (.esd)
│       └── scripts/README.md     on-unit ksh scripts
├── custom/README.md              user-supplied files and where they land
│   └── skins/README.md           what can be reskinned, restore pitfalls
├── dump/README.md                on-card dumps written by the unit
├── toolbox/README.md             support files used by installer and scripts
│   ├── devesd/README.md          stock developer GEM screens
│   └── gem/README.md             GEM 3.x edition of the payload
└── tools/README.md               host-side Python utilities
```

Keeping these useful:

- When a directory is added, renamed or removed in the repo, do the same here.
- Record facts that cannot be worked out quickly from the code (invariants,
  pitfalls, what must stay in sync). Do not restate file contents.
- If a note turns out to be wrong, correct it in the same change.
- These files are documentation only. Nothing on the unit reads them.
