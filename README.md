# DM32-Info

Quick-reference links for the **Baofeng DM-32UV** DMR handheld.

## Docs
- [SPECS.md](SPECS.md) — DM-32UV hardware specs
- [PROGRAMMING-TOOLS.md](PROGRAMMING-TOOLS.md) — OEM CPS vs NeonPlug vs qdmr
- [FIRMWARE.md](FIRMWARE.md) — version history, firmware families, AES-256 constant-IV status, SHA256s, recovery
- [COMMUNITY.md](COMMUNITY.md) — upstream community map (infotex58.ru et al.), OpenDM-32 (OpenGD77 port), hardware RE (HR_C7000/FD6818), firmware mods

> ⚠️ **There is more than one kind of DM-32UV, and flashing the wrong firmware
> soft-bricks the radio.** Every variant reports the same internal model string
> `DP570UV`, so that string cannot tell them apart. The most reliable
> non-disassembly tell is the side buttons: **smooth SK1/SK2 = HR Vocoder
> (`DM32.00.*`)**, **raised tactile ridges = ROW (`DM32.01.*`)**. Verify before
> writing any image — read [FIRMWARE.md](FIRMWARE.md) first.
>
> *Corrected 2026-08-10: this notice previously claimed "three different
> radios." The count was not supported by the source — see
> [FIRMWARE.md](FIRMWARE.md#hardware-families-what-is-actually-established).*

## Provenance convention

This repo aggregates a community archive, forum threads, and reverse-engineering
reports of wildly varying reliability. **Some upstream sources mix first-party
observation with speculation and unattributed LLM output in the same document.**
One bad flash soft-bricks a radio, so claims here carry their evidence class:

| Marker | Meaning |
|--------|---------|
| *(unmarked)* | First-party upstream statement, or independently verified locally (hashes, extracted version strings). |
| 🔶 | **Unverified / speculative.** Attribution given inline. Do not act on it without confirming. |
| ⚠️ | Acting on this incorrectly damages hardware. |

Rules:

1. **Never restate a hedged upstream claim as settled fact.** If the source says
   "unverified," or is an LLM paraphrase, that hedge travels with the claim.
2. **Distinguish firmware groupings from hardware revisions.** Distinct binaries
   may be regional SKUs or vendor rebrands; separate silicon is a stronger claim
   needing separate evidence.
3. **Keep safety warnings independent of contested taxonomy.** The soft-brick
   warning is first-party and must survive any correction to the family model.
4. **Cite the branch or thread**, not just the repo, so a claim can be re-checked.

## Links

| What | Link |
|------|------|
| Product page (Baofeng) | https://www.baofengradio.com/products/dm-32uv |
| Official download area (CPS / firmware / manuals) | https://www.baofengradio.com/pages/download |
| CPS download (community archive) | https://github.com/M7OCM/DM-32UV/tree/CPS |
| Firmware download (community archive) | https://github.com/M7OCM/DM-32UV/tree/DM-32UV-Firmware-(most-common) |
| HR Vocoder firmware (community archive) | https://github.com/M7OCM/DM-32UV/tree/HR-Vocoder-DM-32UV-Firmware |
| Full community archive (M7OCM) | https://github.com/M7OCM/DM-32UV |
| DM32 protocol spec (@infamy) | https://github.com/infamy/DM32-Protocol-Spec |

## Related Repos
- **NeonPlug** — web-based CPS (Web Serial/BLE). Live: https://neonplug.app · Repo: https://github.com/infamy/NeonPlug
- **dmrconfig (DM-32)** — https://github.com/emuehlstein/dmrconfig_dm32
- **qdmr** — GUI DMR programmer (Linux/macOS). Home: https://dm3mat.de/software/qdmr/ · Repo: https://github.com/hmatuschek/qdmr

## OpenDM32 — OpenGD77 ported to the DM-32
Forum thread (primary source, Russian): http://infotex58.ru/forum/index.php?topic=1168.0

An open-firmware replacement for the DM-32, ported from OpenGD77 — the same project family as emuehlstein/OpenGD77_SSRFLite_Generator. Lead: Koshak, building on andynvkz's solo port of OpenGD77 to the Zastone UV008 (same platform — hence the archive's dump tool being named "Open UV008").

Status: thread opened 2025-11-16; alpha as of 2026-07-23 (10 pages), with testers actively flashing and filing bugs. One tester's summary: "The alpha alone is a vast improvement upon the stock firmware and especially the stock CPS which made programming impossible."

Working so far: analog RX/TX (pending VC-TCXO reference correction), GPS, RTC (manual or GPS-set). UI language strings are a separate file compiled in, not baked into the binary.

Two findings from Koshak's RE worth knowing
1. The stock S-meter is broken in FIRMWARE, not hardware. The stock CPU never polls FD6818 registers 0x65 / 0x67, so the chip's real RSSI is simply never read:

u16 RF_GetRssi()  { return RF_Read(0x67) & 0x1FF; }  // bit[8:0], LSB -> 0.5 dB
u8  RF_GetNoise() { return RF_Read(0x65) & 0x7F;  }  // bit[6:0]
Koshak: "S-meter in the transceiver does not work properly. Why the developers did it this way is a mystery to me." The radio can report RSSI at 0.5 dB resolution; Baofeng's firmware just does not ask. Relevant to any signal-quality work.

2. Published GPIO pin map, including ALPU-MP I2C_SCL/SDA and TXD GPS ATGM336H. So the GPS is an ATGM336H, and there is an ALPU-MP crypto/copy-protection part — typically the hard obstacle in ports like this.


