# DM-32UV Firmware

Version tracking and flashing notes for the Baofeng DM-32UV.

**Binaries are not committed here.** They're ~870 KB each and the community
archive already hosts them with better provenance. This file records *which*
image belongs on *which* hardware, plus the SHA256 so you can verify what you
downloaded. Pull from [M7OCM/DM-32UV](https://github.com/M7OCM/DM-32UV).

## Firmware families and the soft-brick hazard

The single most important thing on this page: **there is more than one kind of
DM-32UV, and flashing the wrong firmware soft-bricks it.** Radios report the
same internal model string `DP570UV` regardless, so the model string is *not*
enough to tell them apart.

> **Correction (2026-08-10).** An earlier revision of this page asserted "three
> hardware lines" as settled fact. That was wrong on two counts and is retracted.
> See [Hardware families: what is actually established](#hardware-families-what-is-actually-established).

### Firmware families (established — version strings verified locally)

These are **firmware** groupings, taken from the archive's branch layout and
confirmed by extracting version strings from the binaries.

| Firmware prefix | Files | Archive branch | Notes |
|-----------------|-------|----------------|-------|
| `DM32.01.*` | `DM32.01.01.{032,037,039,040,046,047,049}`, `DM32.01.02.046`, `DM32.01.L01.048` | [`DM-32UV-Firmware-(most-common)`](https://github.com/M7OCM/DM-32UV/tree/DM-32UV-Firmware-(most-common)) | Most common type, sold via AliExpress/Banggood etc. |
| `DM32.NRF.*` | `DM32_NRF_049_20251017.bin` → `DM32.NRF.01.049`<br>`DM32_Fanti_049_20251106.bin` → `DM32.NRF.02.049` | **same branch as above** | Sourced from Taiwan for the FT-399DMR / DM-32 via [rowa.com.tw](https://www.rowa.com.tw/store/index.php?route=product/product&path=134&product_id=1731). |
| `DM32.00.*` | `DM32.00.01.{034,037,046}HRVocoder.bin` | [`HR-Vocoder-DM-32UV-Firmware`](https://github.com/M7OCM/DM-32UV/tree/HR-Vocoder-DM-32UV-Firmware) | Separate branch. Archive reports early builds used `01.01`, and some used a `UV32.*` prefix instead of `DM32.*`. |

⚠️ **`DM32.NRF.*` lives in the same branch as `DM32.01.*`**, and the archive
describes those files only by where they were *sourced*, not by distinct
hardware. Do not read the NRF/Fanti row as a separate hardware line — that was
the specific error in the earlier revision of this page.

### Hardware families: what is actually established

**Two groupings are supported by the archive's own structure**, in that `00.*`
and `01.*` are maintained on separate branches with mutual soft-brick warnings:

- `DM32.01.*` — "ROW" / most common
- `DM32.00.*` — "HR Vocoder" / "Taiwan version"

**What is NOT established:**

- **The count.** The archive's own text says **two** distinct versions, not
  three. Three *firmware prefixes* is not three *hardware revisions* — distinct
  binaries are equally consistent with regional SKUs or vendor rebrands.
- **The HR-C6000 vocoder attribution.** 🔶 The claim that the `00.*` variant uses
  the HR-C6000 chipset, was developed with a Taiwanese partner, and is
  restricted by DVSI vocoder IP comes from a block in the archive's HR-Vocoder
  README explicitly headed *"Additional (unverified) information gleaned from
  ChatGPT"* and closing with *"make of that what you will!??"*. **Treat as
  unverified LLM output, not as a hardware fact.** The plausible-sounding
  reduced-distribution rationale is part of that same unverified block.
  Board-level RE on ROW units identifies the main chip as the **`HR_C7000`**
  (CK803S C-SKY core) — see [COMMUNITY.md](COMMUNITY.md) — which is first-party
  evidence for ROW but leaves the C6000-on-HR-Vocoder claim untested.
- **Clean separation by board rev.** The archive groups `DM32_UV_V1.2 2024-08-05`
  as "HR Vocoder **and early non-HRV**" — the same board rev spans both, which
  argues against a tidy split.

### Telling them apart without opening the radio

- **Side buttons** (first-party archive observation, with ASCII art in the
  HR-Vocoder README). The Asia-only / HR Vocoder version has **smooth** SK1/SK2;
  the ROW model has raised tactile horizontal ridges. **This is the best
  non-disassembly tell we have**, with a provenance caveat: the smooth ↔
  HR-Vocoder half is a **single-source claim** — the archive maintainer's own
  prose (above the README's "unverified ChatGPT" block, so not LLM output, but
  uncorroborated anywhere else as of 2026-09-12). The ridged ↔ ROW half is
  hardware-verified on two radios (both ridged, both running `DM32.01.*`).
  Treat "ridged ⇒ safe to flash `01.*`" as solid; treat smooth buttons as
  "assume HR Vocoder, do not flash `01.*`" — which fails safe either way.
- **Installed firmware string.** `DM32.01.*` → most common. `DM32.00.*` /
  `UV32.*` → HR Vocoder branch. `DM32.NRF.*` → Taiwan-sourced, but see the
  warning above before inferring hardware from it.
- **Board revs seen** (list not complete, per archive): `DM32_UV_V1.2 2024-08-05`
  (HR Vocoder and early non-HRV) · `DM32_UV_V1.4 2024-12-20` (common PCB only).
  Boards from 2025 onward may differ.
- The build-date string inside the binaries is `2022-06-27` across every image
  and is **useless** for identification.

### The soft-brick warning stands on its own

This is **first-party archive text on both firmware branches**, independent of
any of the hardware-taxonomy uncertainty above:

> If installed on the wrong hardware (ie the HR Vocoder version) it will soft
> brick the radio, solution is to remove battery, turn device on while pressing
> SK1 and PTT (reattach battery) and reflash the correct version.

So: **the correction above does not soften the flashing risk.** Verify SK1/SK2
before writing any image.

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
| `DM32.01.01.049` | ✓ restored | 50,000 | Latest in the M7OCM archive. Also appearing on newly-sold radios. |

🔶 A newer build, `DM32_049_20250905.bin` (2025-09-05), circulates on
infotex58.ru and is not on Baofeng's site — see [COMMUNITY.md](COMMUNITY.md).
Version string and diff against the archive's `049` unchecked here.

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

## OpenGD77 / OpenDM32 — released, and flashable from macOS

**Status change (2026-09-17): this is no longer "watch the thread".** Builds are
published, and we have flashed one end-to-end. See [COMMUNITY.md](COMMUNITY.md) for the
project background; this section is the firmware and flashing record.

First-hand result: a `DM32.NRF.01.049` ROW unit (SK1/SK2 ridged) flashed to
`OpenGD77_DM32_20260711_addid_2018.bin` and booted into OpenGD77. Whole job done on
**macOS 26.6 / Apple Silicon** — no Windows, no VM.

### Builds

Attachments on [infotex58.ru topic 1168](http://infotex58.ru/forum/index.php?topic=1168.0).
That host is slow; allow a 90 s timeout before concluding it is down.

```
2f5419552a9e365351f8b857dd528e07032ec96a4bb5b73ab1f6d764942fc5ba  OpenGD77_DM32_20260711_addid_2018.bin
9a07f7d878e5bd0dd3f7a97b280cb455657e9474f77b54a0e7b2e48d4976dcc3  OpenGD77_DM32_20260704.bin
```

Both 830,842 B; both report `OpenGD77_HS v0.1.18` in `strings`. The `addid` build adds
DMR ID support and is the newer of the two. The English install PDF is a separate
attachment (zip sha256 `1679de3c5f2872c8308825fd675a8734b6e166db214133141617fea00298f6d1`).

⚠️ Author's own assessment: the firmware *"contains a number of bugs and incomplete
implementation of all functionality, but OpenGD77 is enough to demonstrate how it
works."* Treat as demo-grade. Keep a stock image for the exact build you replaced.

**Container gotcha:** these images are wrapped in the **Baofeng container** — first nine
bytes are `4246555633322d5632` (`BFUV32-V2`), identical to stock. Loaders therefore
report them as "official Baofeng firmware" and seek to `0x100`. That is correct; do not
strip the header.

### Tooling — pure Python, so macOS/Linux work

[`rogerclarkmelbourne/DM32`](https://github.com/rogerclarkmelbourne/DM32) (Roger Clark
VK3KYY, the OpenGD77 lead) publishes the C7000 reverse engineering as plain Python 3 +
pyserial — imports are only `serial, time, os, sys`, with **no Windows-only step** for
backups or firmware loading. `python/` holds `C7000_read_progmem.py`,
`C7000_read_Q128.py`, `C7000_write_Q128.py` and `DM32_firmware_loader.py`; `CPS_HACKED/`
holds an OEM CPS with the Adjust-Mode password stripped (press Return at the prompt).

Codeplug writing does **not** need Windows either — see below.

### Codeplug programming from macOS / Linux: the web CPS

**[grid.radio/opengd77](https://grid.radio/opengd77)** lists **DM32 / UV008 (Baofeng
DM-32)** as a first-class target and drives it over **Web Serial**, so there is no
driver, no Wine and no VM. It handles both **codeplug programming and firmware
flashing**, and documents the same update-mode entry we use: *"Hold PTT + SK1 together
while turning on (green LED = update mode)"*.

Its own notes call out that the DM-32 / UV008 needs no driver on any platform — unlike
the MK22/STM32 radios (GD-77, DM-1801, RD-5R, MD-UV380, MD-9600, DM-1701), which use
WebUSB and need Zadig on Windows. The DM-32 path is a plain USB serial port.

**Requires Chrome, Edge or Brave.** Firefox and Safari do not implement Web Serial.

It imports **CSV** for channels, zones, contacts/TGs and TG lists, which means
[`OpenGD77_SSRFLite_Generator`](https://github.com/emuehlstein/OpenGD77_SSRFLite_Generator)
feeds it directly and the whole chain stays off Windows:

```
SSRF-Lite YAML → OpenGD77_SSRFLite_Generator → CSV → grid.radio/opengd77 → radio
```

It also imports CHIRP CSV and RadioReference exports, and can pull a region of the
RadioID database server-side.

> Third-party hosted tool. We have verified its stated DM-32 support and feature set,
> not its source. Treat codeplug contents accordingly, and keep the OEM CPS
> `E2026.07.13.01` as the reference implementation.

**For radios still on stock firmware**, `qdmr` has native DM-32UV support
(`lib/dm32uv.cc`, `dm32uv_codeplug.cc`, `dm32uv_callsigndb.cc`) and runs on macOS and
Linux — see [PROGRAMMING-TOOLS.md](PROGRAMMING-TOOLS.md). That driver speaks the **stock**
codeplug format, so it does **not** apply to a radio converted to OpenGD77.

### Two upstream scripts abort on healthy radios

Both send their opening handshake exactly once and `assert` on the reply. On macOS +
CH340 the **first write is reliably swallowed** while the radio's UART wakes, so they
die on a radio that is working perfectly. Patch both to retry; retrying costs nothing
because neither has written to the radio at that point.

| Script | Line that fails | Handshake | Good reply |
|---|---|---|---|
| `DM32_read_Q128.py` | first `assert` | `PSEARCH` | `06 44 50 35 37 30 55 56` (`\x06DP570UV`) |
| `DM32_firmware_loader.py` | first `assert` | `0x52` | `0x06` |

Keep everything from the erase command onward byte-identical. **Never** add retries
around the erase or the block-write loop.

`C7000_read_progmem.py` has the same brittleness mid-transfer: one dropped byte fails
`assert data[:2] == b'\x02\x05'` and kills a run that is 6 % done. Wrapping each
256-byte block in up to 8 retries with an input-buffer resync fixed it — on a good run
the retry counter stays at **0**, so a flaky read means the cable was disturbed, not
that the line is noisy.

### Back up before flashing — two chips, one irreplaceable

| Dump | Script | Size | Radio state |
|---|---|---|---|
| GD25Q128 main flash | `DM32_read_Q128.py` | 16,777,216 B | **on**, stock firmware, cable attached |
| C7000 program memory | `C7000_read_progmem.py` | 1,048,576 B | **off** at start, then power on normally |

The **Q128 dump holds per-radio RX/TX calibration and the ALPU key — no vendor image can
restore it.** Take it first. It runs over the stock CPS protocol and needs no boot-time
handshake; budget ~25 min at 115200. Sanity check: exact size, and mostly `0xFF`
(88 % erased on our unit) with a few hundred populated 4 KB blocks, including around
`0xa7000` — which is where upstream's own commented-out `start_addr` points.

The progmem dump is the firmware region, so a byte-exact vendor image is an acceptable
substitute if a unit will not cooperate. It needs the C7000 init burst `02 24 00 03`,
which is emitted **only at power-on with the cable already attached**. Despite the
README's "30 seconds", the wait loop is unbounded, so there is no race.

### 🔴 Cable order: green LED first, cable second

The single biggest time-waster. **Many units will not boot normally with the programming
plug seated** — the screen stays dark and the radio looks dead while it is in fact
talking. Judge by script output, never by the screen.

For the flash specifically:

1. Detach the cable **at the radio end only** — leave USB in the host so the bridge does
   not re-enumerate.
2. Hold **PTT + SK1** while powering on, until the **green LED** lights.
3. **Now** plug the cable into the radio.

With the cable seated before update mode, the bootloader never answers — ten consecutive
`no response` on the init byte, indistinguishable from a wrong button combo.

**Every replug re-enumerates the USB bridge under a new device node**
(`/dev/cu.usbserial-2120` → `-2110`). Anything bound to the old node dies with
`OSError: [Errno 6] Device not configured`, which also reads exactly like a dead radio.
Re-check the node after any cable event.

CH340 (`idProduct` `29987` / `0x7523`), FTDI and CP2102 are all supported;
`ERROR: communication` means swap the cable.

### A good flash run

```
bootloader ACK on attempt 1
Official Baofeng firmware detected.
Send Erase command. Waiting to for up to 15 seconds for this to finish
Erase complete
Sending firmware data
1% … 99%
Data send complete
Send Reboot command
```

~5 min for an 830 KB image, 812 × 1 KB blocks with CRC16-XMODEM; the last third runs
slower than the first. Background it and poll rather than setting a kill-timeout.

Verify first boot with the cable **detached at the radio end** and a normal power-on.
OpenGD77 announces itself with a **`Settings Updated`** screen on the first boot after a
version change — stock firmware never shows it, so that message alone confirms the port
is running. Expect channels to be empty or garbage afterwards: the 16 MB flash still
holds a Baofeng-layout codeplug until CPS writes an OpenGD77 one.

### Reverting

Flash the stock image for the exact build you replaced, the same way. Verify the image
first with `strings -a IMAGE.bin | grep -Eo 'DM32[._][A-Za-z0-9._]*' | sort -u` and match
it against the [SHA256](#sha256) table above. The bootloader lives in a separate region
and survives a failed or interrupted firmware write.

### Do not try to build it

There is no public OpenGD77 firmware source (checked 2026-09-17):
`rogerclarkmelbourne/OpenGD77` returns 404 and no repo exists under that account,
SourceForge has no `p/opengd77` project, `opengd77.com/downloads/` is an empty static
husk, and the only GitHub mirror, [`open-ham/OpenGD77`](https://github.com/open-ham/OpenGD77),
is frozen at 2022-12 with zero `DM32`/`C7000` hits. The porter's tree is unpublished.
Flash a released `.bin` or nothing.

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
