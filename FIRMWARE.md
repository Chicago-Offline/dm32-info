# DM-32UV Firmware

Version tracking and flashing notes for the Baofeng DM-32UV.

**Binaries are not committed here.** They're ~870 KB each and the community
archive already hosts them with better provenance. This file records *which*
image belongs on *which* hardware, plus the SHA256 so you can verify what you
downloaded. Pull from [M7OCM/DM-32UV](https://github.com/M7OCM/DM-32UV).

## Three hardware lines, three firmware families

The single most important thing on this page: **DM-32UV is not one radio.**
Flashing the wrong family soft-bricks it. All three families report the same
internal model string `DP570UV`, so the firmware string is *not* enough to tell
them apart.

| Family | Firmware prefix | Hardware | Archive branch |
|--------|-----------------|----------|----------------|
| ROW / common | `DM32.01.*` | Common PCB, sold via AliExpress/Banggood etc. | [`DM-32UV-Firmware-(most-common)`](https://github.com/M7OCM/DM-32UV/tree/DM-32UV-Firmware-(most-common)) |
| Taiwan / rowa | `DM32.NRF.*` | FT-399DMR / DM-32, sourced from [rowa.com.tw](https://www.rowa.com.tw/store/index.php?route=product/product&path=134&product_id=1731) | same branch, `*_NRF_*` / `*_Fanti_*` files |
| HR Vocoder | `DM32.00.*` (early ones `01.01`, some `UV32.*`) | HR-C6000 vocoder chipset, "Taiwan version", different chip + hardware | [`HR-Vocoder-DM-32UV-Firmware`](https://github.com/M7OCM/DM-32UV/tree/HR-Vocoder-DM-32UV-Firmware) |

### Telling them apart without opening the radio

- **Check the installed firmware string first.** `DM32.01.*` → ROW.
  `DM32.NRF.*` → Taiwan. `DM32.00.*` / `UV32.*` → HR Vocoder.
- **Side buttons.** The HR Vocoder / Asia-only version has **smooth** SK1/SK2.
  The ROW model has raised tactile horizontal ridges.
- **Board revs seen** (list not complete, per archive): `DM32_UV_V1.2 2024-08-05`
  (HR Vocoder and early non-HRV) · `DM32_UV_V1.4 2024-12-20` (common PCB only).
  Boards from 2025 onward may differ.
- The build-date string inside the binaries is `2022-06-27` across every image
  and is **useless** for identification.

## Version history (ROW / `DM32.01.*`)

| Version | Record function | CSV contacts | Notes |
|---------|-----------------|--------------|-------|
| `DM32.01.01.032` | — | — | Earliest in the archive. |
| `DM32.01.01.037` | — | — | |
| `DM32.01.01.039` | — | — | |
| `DM32.01.01.040` | — | — | |
| `DM32.01.01.046` | — | — | Subject of the 2025 static-disassembly claim that the constant-IV code path was gone. Never verified on air. |
| `DM32.01.01.047` | — | — | Appearing on newly-sold radios as of late 2025. |
| `DM32.01.L01.048` | ✗ removed | **150,000** | Baofeng "test" firmware. Traded the record function for extra CSV contact memory. |
| `DM32.01.01.049` | ✓ restored | 50,000 | Current latest. Also appearing on newly-sold radios. |

`049` also reportedly adds an analog frequency-copy mode. Community reports of
low DMR audio on the newest firmware even at max digital mic gain — unconfirmed.

**The 048 → 049 tradeoff is a real regression if you carry a big contact list:**
150K → 50K. Check your CSV size before upgrading.

### Taiwan / `DM32.NRF.*`

| File | Internal version |
|------|------------------|
| `DM32_NRF_049_20251017.bin` | `DM32.NRF.01.049` |
| `DM32_Fanti_049_20251106.bin` | `DM32.NRF.02.049` |

These two differ from each other across ~734 KB — separate firmware lines, not
rebuilds of one image.

### HR Vocoder / `DM32.00.*`

Archive holds `034`, `037`, `046` only. **There is no 049 for this hardware.**
Firmware for this variant isn't widely distributed — the HR-C6000 vocoder IP is
DVSI's, and redistributing firmware containing it is legally fraught, so most
communities avoid hosting it.

## AES-256 constant-IV bug

DMRA AES-256 is AES in OFB mode with a 32-bit IV sent in the PI Header (the `MI`
field). If `MI` never changes, the same keystream is reused on every transmission
under a given key, the construction degrades to a Vigenère-equivalent stream
cipher, and roughly 30 captured transmissions are enough to recover the keystream
using silence frames as known plaintext.

**Status by firmware:**

| Firmware | Constant-IV status |
|----------|--------------------|
| 2023–2024 builds (DR-1802U, DM-32UV) | **Vulnerable** — constant `MI`, reproduced across multiple radios. |
| `DM32.01.01.046` | Static disassembly suggested the code path was gone. **Never verified on air.** |
| `DM32.NRF.01.049` | **Verified fixed on air** — see below. |
| `DM32.01.01.049` (ROW) | **Unverified.** Presumed fixed; not tested. |

### The 049 verification

[RadioReference thread](https://forums.radioreference.com/threads/dm-32uv-aes-256-constant-iv-bug-verified-fixed-in-firmware-049-on-air-test-36-captures.500405/)
— 36 PI Header captures, two runs ~3h apart, `DM32.NRF.01.049`, dsd-fme +
RTL-SDR, AES-256 with an all-zeros test key on 446.500 MHz.

Result: all 36 `MI` values unique, uniformly distributed across the 32-bit space,
zero overlap between runs. OFB chaining is the canonical 32-bit
shift-and-append, and the last word of the final PI continuation equals the next
call's seed. Radio signals **ALG ID 0x25** (DMRA-standard AES-256), not
Baofeng's proprietary PC5-256 "Advanced Privacy". Verdict: the classical
keystream-reuse attack does not work against this firmware.

**Scope — what it does not establish:** end-to-end cipher correctness (varying
`MI` is necessary, not sufficient), that the 32→128-bit IV expansion follows
DMRA spec, or cross-vendor voice interop. The author could not get intelligible
audio out of dsd-fme/mbelib in *any* configuration — including clear voice with
encryption disabled — despite zero reported AMBE structural errors. Open
question, and it wants a reference HT (AnyTone D878UVII, BTECH DMR-6X2 Pro, TYT
MD-UV390) with a matching AES key to close.

**⚠️ The verified image is the Taiwan one.** The on-air test ran on
`DM32.NRF.01.049`. If your radio is ROW hardware your upgrade path is
`DM32.01.01.049`, a *different firmware line* (~790 KB of differing bytes vs the
NRF build). Reasonable to assume the fix landed in both — but that's inference,
and precisely the kind the original post was careful to avoid making.

Baofeng has never publicly acknowledged the bug or claimed a fix in any release
notes. Identifying exactly when it landed would mean bisecting forward from 032.

## SHA256

Verify anything you download. Captured 2026-08-07 from the
`DM-32UV-Firmware-(most-common)` branch.

```
03107b6f600e7fa95105af8324cff349c0f17fe30db249c14d11e79baa8da97a  DM32.01.01.049.bin
fda860febfcf1a234eed7fa73272112891074aac83746e4f8dfe224a2a700f8f  DM32.01.L01.048.bin
a8a5fc80e7116e8e8c9926f9c5e6d56a72689d70e09db3f0fec57b3a01ec4a31  DM32_Fanti_049_20251106.bin
aff6b4dc09dcce45847173defba26e94157cc628af86826e21e5af227bdc52dd  DM32_NRF_049_20251017.bin
```

Sizes: `01.01.049` 869,688 B · `01.L01.048` 860,416 B ·
`NRF_049` 873,964 B · `Fanti_049` 874,028 B.

## Recovery and reset

**Soft-brick recovery** (wrong-hardware flash): remove battery, hold **SK1 +
PTT**, reattach battery and power on, then reflash the correct image. Survivable.

**Factory reset:** upload a CPS file with *allow reset* selected, then power on
holding **SK1 + SK2** to reach the reset menu.

**Flash dump:** the archive's `OpenUV008_Flash_v2.zip` / `open_uv008.zip` reads
firmware off the radio (also works on the Zastone UV008 — same platform, hence
the name). Edit the `.ini` for your paths first, select **GD25Q16** for
read/write SPI flash, pick the COM port, **turn the radio off**, then read.

## CPS

Community archive: [`CPS` branch](https://github.com/M7OCM/DM-32UV/tree/CPS).
Pair the newest CPS with the newest firmware.

| CPS | Size | SHA256 |
|-----|------|--------|
| `BAOFENG_DM-32UV_CPS_v1.60_20260608.exe` | 11.7 MB | `78f9dbce2815057188fd8de6c99b842ba2359745ddd976adef5053293472fd1c` |
| `CPS DMR Radio Setup v1.50.exe` | 4.5 MB | `1f2f8735777c501fc77c1bdd8e915124fff09fca2831e2771be77429c4055441` |

Archive also carries v1.22 through v1.45. The 4.5 MB → 11.7 MB jump between 1.50
and 1.60 is a rebuild, not a patch — worth keeping 1.50 as a fallback, since the
archive's own warning is that the CPS is *"extremely buggy, even the latest, and
the various firmware revisions are too, some features do not work."*

**All OEM CPS builds are Windows PE32.** No macOS path — you need Windows or a VM
with USB passthrough, and passthrough is historically flaky for radio
programming. See [PROGRAMMING-TOOLS.md](PROGRAMMING-TOOLS.md) for NeonPlug and
qdmr as cross-platform alternatives.

### CPS passwords

From the archive's CPS README:

- **Embedded Information** — `374612`
- **Engineering / Adjust Mode** — `66660501`

⚠️ **Adjust Mode is RF calibration.** Chinese-only UI, no English option. Save a
copy of every screen (or export to file) *before* changing anything. The archive
ships a `default-adjust-mode.test` file marked use-at-own-risk — calibration is
per-radio, so loading someone else's values is a worse outcome than a soft brick.

## qdmr DM-32UV patches

The RadioReference author reports qdmr **v0.15.0** needs three local patches to
write DM-32UV codeplugs correctly:

1. `DM32UV` missing from the USB autodetect dispatch
2. RTS asserted at port open breaks the programming protocol
3. `cli/verify` has no DM-32UV case

He intends to submit these upstream and will share them on request. This matches
independently-observed qdmr 0.15.1 DM32 write-path failures. The RTS-at-port-open
issue is the same class of problem seen with CP2102-based interfaces on macOS,
where opening the port asserts RTS.

## Sources

- [M7OCM/DM-32UV](https://github.com/M7OCM/DM-32UV) — CPS, firmware, flash dump tool, mods
- [RadioReference: 049 AES-256 constant-IV verification](https://forums.radioreference.com/threads/dm-32uv-aes-256-constant-iv-bug-verified-fixed-in-firmware-049-on-air-test-36-captures.500405/)
- [Baofeng official download area](https://www.baofengradio.com/pages/download)
- [OpenDM32 forum thread](http://infotex58.ru/forum/index.php?topic=1168.0) (Russian) — open-firmware port, see [README](README.md)
- Thanks to RA4FHE for the research behind OpenDM32.
