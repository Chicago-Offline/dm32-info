# Community sources, reverse engineering, and firmware mods

Survey of the non-English (mostly Russian) DM-32UV community, first compiled
2026-09-12. Follows the repo [provenance convention](README.md#provenance-convention).

**The key fact: the M7OCM GitHub archive is downstream.** The primary source for
DM-32 firmware work is **infotex58.ru** (Russian hobby forum, Penza), where user
**Koshak** does board-level reverse engineering and firmware modding. Modded
images circulating in the Facebook DM-32 group, Whirlpool (AU), and
RadioReference all trace back there.

## Source map

| Source | What it holds |
|--------|---------------|
| [infotex58.ru topic 1148](http://infotex58.ru/forum/index.php?topic=1148.0) | Main DM-32 thread, 615+ posts. Firmware mods, new builds, CPS tricks. |
| [infotex58.ru topic 1168](http://infotex58.ru/forum/index.php?topic=1168.0) | **OpenDM-32** — OpenGD77 port to the DM-32 (see below). |
| [infotex58.ru topic 1155](http://infotex58.ru/forum/index.php?topic=1155.0) | Internal-memory dumper. |
| [gzalo/dm32-uv](https://github.com/gzalo/dm32-uv) | Best English/Spanish index (LU6CGA): bootloader ROM docs, voice-prompt swapping, bitmap font extraction, harmonics measurements, service manual for a same-micro radio (schematics + calibration), CHIRP→CPS CSV converter. |
| [yo3hjv/Baofeng-DM32](https://github.com/yo3hjv/Baofeng-DM32) | YO3HJV notes (Romania). |
| [M7OCM/DM-32UV](https://github.com/M7OCM/DM-32UV) | The community archive this repo already documents — downstream aggregation of the above. |
| [vrtp.ru topic 33914](https://vrtp.ru/index.php?showtopic=33914&st=0) | Second Russian thread. |
| [RadioReference UV32/DM32 thread](https://forums.radioreference.com/threads/baofeng-uv32-dm32.484874/) | English-language relay of the RU mods; W4KRR's independent hex-mod verification. |

## OpenDM-32: OpenGD77 port — **released and flashable** (updated 2026-09-17)

Koshak's port of **OpenGD77 to the DM-32** ([topic 1168](http://infotex58.ru/forum/index.php?topic=1168.0),
started 2025-11-16) has shipped binaries. Builds on **andynvkz's completed OpenGD77 port
to the Zastone UV008** — the same platform (the UV008 flash-dump tool working on the
DM-32 was already documented here).

**We have flashed it.** A `DM32.NRF.01.049` ROW unit now runs
`OpenGD77_DM32_20260711_addid_2018.bin` (`OpenGD77_HS v0.1.18`), flashed entirely from
macOS. Procedure, hashes, gotchas and the revert path are in
[FIRMWARE.md → OpenGD77 / OpenDM32](FIRMWARE.md#opengd77--opendm32--released-and-flashable-from-macos).

Earlier status notes, now superseded: hardware bring-up essentially done; analog RX/TX
working (demo video posted); GPS (ATGM336H) working; RTC settable manually or via GPS;
per-language text files compiled in.

> ⚠️ Still demo-grade — the author describes *"a number of bugs and incomplete
> implementation of all functionality"*. It does not yet moot the OEM firmware tradeoffs
> (048/049 record-vs-contacts regression, the AES question); it makes them optional.

**Roger Clark VK3KYY (the OpenGD77 lead) is directly involved.**
[`rogerclarkmelbourne/DM32`](https://github.com/rogerclarkmelbourne/DM32) publishes his
C7000 reverse engineering as pure-Python flash read/write and firmware-loader tools,
plus an OEM CPS with the Adjust-Mode password stripped. He has also added **DM32/UV008
support to OpenGD77 CPS** (`E2026.07.13.01`) — note that build is hosted only on
infotex58.ru; the official mirror still caps at `E2026.05.26.01`.

❌ **The firmware source remains unpublished** — `rogerclarkmelbourne/OpenGD77` 404s, no
SourceForge project exists, and the only GitHub mirror is frozen at 2022-12. Binary-only.

## Hardware findings (Koshak board-level RE, ROW units)

- **Main chip is `HR_C7000`** — CK803S C-SKY 32-bit RISC core (Alibaba), 288 KB
  RAM, external SPI flash. A detailed Chinese datasheet circulates via
  [connectsystems.com](https://www.connectsystems.com/products/top/radios/CS120D/HR_C7000%20Document%202.pdf).
  🔶 Note the tension with the archive's unverified-ChatGPT claim that the "HR
  Vocoder" variant uses the HR-C6000: Koshak's RE is on ROW units, so both could
  be true, but the C6000 attribution remains unverified while HR_C7000-on-ROW is
  first-party RE.
- **RF chip is `FD6818`.** The notoriously useless S-meter is a firmware
  omission, not hardware: OEM firmware **never polls RSSI register `0x67`
  (bit[8:0], 0.5 dB LSB) or noise register `0x65` (bit[6:0])**.
- **ALPU-MP security/licensing chip on I2C** — likely the vocoder-IP enforcement.
- LCD is probably an ILI9341. GPS is an ATGM336H.
- Factory initial flash is via **JTAG**. CH340 programming cables work with the
  Baofeng bootloader/CPS but **cannot** talk to the HR_C7000 ROM bootloader
  (bootloader docs in gzalo/dm32-uv).

## Firmware modding

**The firmware is unencrypted and unhashed** — plain hex edits flash fine.

### Band-limit hex mod (ROW `01.*`)

Verified independently by W4KRR ([RadioReference post #102](https://forums.radioreference.com/threads/baofeng-uv32-dm32.484874/page-6)):
band limits are **little-endian uint32 frequencies in 10 Hz units**, present
twice in the firmware `.bin` and also in the CPS `.exe` (edit both):

| Bytes | Decodes to |
|-------|-----------|
| `C0 29 CD 02` | 470 MHz (stock cap; 2 instances) |
| `80 F0 FA 02` | 500 MHz |
| `00 40 0D 03` | 512 MHz |

⚠️ Standard soft-brick rules apply; back up the original `.bin` and `.exe`.

### Koshak's extended-band 046 build

`DM32.01.02.046_mod_220-520MHz_avia_23-136.bin` — adds 23–136 MHz RX (AM
airband) and 220–520 MHz. Widely redistributed (Facebook group files section,
Whirlpool AU). Reports:

- 470–520 RX confirmed by multiple users; 477 MHz AU UHF CB TX confirmed.
- Startup image is lost (voltage display works as a substitute).
- Stock CPS won't enter out-of-band frequencies directly — CSV export → edit →
  import works, and the CPS accepts the channels thereafter.
- 🔶 **220 MHz reports conflict**: one user reports TX/RX works with poor RX
  sensitivity; W4KRR reports display-only, no RX/TX. Unresolved — the RF
  front-end may simply not cover it usefully.

### Newer 049 build

🔶 `DM32_049_20250905.bin` (2025-09-05) circulates on infotex58
([topic 1148 post](http://infotex58.ru/forum/index.php?topic=1148.msg10422#msg10422))
and is **not** on Baofeng's site and **postdates the M7OCM archive's 049**.
Version string and diff against archive `049` not yet checked here.

### CPS tricks

- Hidden regional flag: **`cps.ini` → `russia=1`** changes CPS behavior
  (discussed on infotex58/Telegram; specifics not documented here yet).

## Not yet chased

- vrtp.ru thread contents (registration-walled in places).
- Diffing `DM32_049_20250905.bin` against archive `DM32.01.01.049`.
- Whether OpenDM-32 will support the HR-Vocoder (`00.*`) line at all.
- hellocq.net (CN) and Taiwan forums: nothing DM-32-specific surfaced in
  indexed search 2026-09-12; the CN domestic scene appears to live on
  Douyin/WeChat, which don't index. One Douyin teardown/programming video
  suggests 🔶 the CN-domestic SKU ships TX-locked to 144–148 / 430–440 MHz.
