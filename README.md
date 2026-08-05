# DM32-Info

Quick-reference links for the **Baofeng DM-32UV** DMR handheld.

## Docs
- [SPECS.md](SPECS.md) — DM-32UV hardware specs
- [PROGRAMMING-TOOLS.md](PROGRAMMING-TOOLS.md) — OEM CPS vs NeonPlug vs qdmr

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


