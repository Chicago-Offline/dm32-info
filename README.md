# DM32-Info

Quick-reference links and version tracking for the **Baofeng DM-32UV** DMR handheld.

> Bare stub for now — links + current versions. Expand later with codeplug notes, Chicago mesh/DMR profiles, and firmware mod notes.

## Links

| What | Link |
|------|------|
| Product page (Baofeng) | https://www.baofengradio.com/products/dm-32uv |
| Official download area (CPS / firmware / manuals) | https://www.baofengradio.com/pages/download |
| CPS download (community archive) | https://github.com/M7OCM/DM-32UV/tree/CPS |
| Firmware download (community archive) | https://github.com/M7OCM/DM-32UV/tree/DM-32UV-Firmware-(most-common) |
| HR Vocoder firmware (community archive) | https://github.com/M7OCM/DM-32UV/tree/HR-Vocoder-DM-32UV-Firmware |
| Full community archive (M7OCM) | https://github.com/M7OCM/DM-32UV |
| DM32 protocol spec (reverse-engineered, @infamy) | https://github.com/infamy/DM32-Protocol-Spec |

> The official Baofeng download area lists the DM-32UV under its own section; grab CPS + firmware there for the "blessed" builds. The M7OCM archive mirrors current + historical CPS/firmware and is the best single source for older/test builds.

## Current Versions

_Last checked: 2026-08-04 — verify against the official/archive links above before flashing; version strings drift and community archives move faster than the official page._

| Component | Version (best known) | Notes |
|-----------|----------------------|-------|
| CPS | Latest from Baofeng download area | Known to be buggy across all revisions per community reports — keep backups. |

### Board / Case Revisions
- `DM32_UV_V1.2` (2024-08-05) — HR Vocoder DM-32UV + early non-HRV PCB
- `DM32_UV_V1.4` (2024-12-20) — common DM-32UV PCB revision
- 2025+ boards may differ. Recent case rev: narrower display bezel (improvement over earlier wide-bezel units).

## Related Chicago Offline Repos
- **NeonPlug** — web-based CPS (Web Serial/BLE), programs the DM-32UV / DP570UV. Live: https://neonplug.app · Repo: https://github.com/infamy/NeonPlug
- [`emuehlstein/dmrconfig_dm32`](https://github.com/emuehlstein/dmrconfig_dm32) — dmrconfig fork adding DM-32 support (superseded by the infamy protocol spec).
- [`emuehlstein/qdmr`](https://github.com/emuehlstein/qdmr) — GUI DMR programmer (Linux/macOS).

---
*Radio params for Chicagoland mesh differ from DMR — this repo is the DMR-radio reference, not the LoRa mesh config.*
