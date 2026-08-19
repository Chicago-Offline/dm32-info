# DM-32UV Specifications

Source: [Baofeng product page](https://www.baofengradio.com/products/dm-32uv). Verify against the official listing / manual before relying on any value.

| Spec | Value |
|------|-------|
| Type | Dual-band DMR + analog handheld |
| RX frequency | VHF 136–174 MHz · UHF 400–480 MHz · AM 108–136 MHz · FM (broadcast) 65–108 MHz |
| TX frequency | 136–174 MHz · 400–470 MHz |
| Output power | High 10 W / Mid 4 W / Low 1 W |
| Memory channels | 4,000 |
| Digital contacts | 50,000 |
| Digital modes | DMR Tier I & II |
| Analog | FM |
| GPS / APRS | Yes (digital APRS) |
| Programming connector | USB-C |
| FCC | Part 90 accepted — FCC ID `2AJGM-NA32UV` |

> Frequency ranges vary by firmware and regional variant; community firmware can widen usable ranges. TX only where you're licensed.

## Codeplug Structure / Limits

Counts below are from the [DM-32UV operating manual](https://www.manualslib.com/manual/3667288/Baofeng-Dm-32uv.html) and Baofeng's official [DM-32UV programming blog series](https://www.baofengradio.com/blogs/news/programming-the-baofeng-dm-32uv-part-four-channel-scanning). Some are firmware/CPS-version dependent (noted). **Validate against the OEM CPS UI** for the firmware/CPS you're running — marked `?` where unconfirmed.

| Structure | Count / Limit | Notes |
|-----------|---------------|-------|
| Channels (total) | 4,000 | Analog + digital combined. |
| Zones | 250 | Confirmed in OEM CPS v1.59 (zones 1–250). No default "all channels" zone — a channel must be in ≥1 zone to appear on the radio. |
| Channels per zone | 64 | Confirmed in OEM CPS v1.59 (channels 1–64 per zone). Analog and/or digital. |
| Scan lists | 32 | Confirmed in OEM CPS v1.59 (scan lists 1–32). A channel may appear in any number of scan lists. |
| Channels per scan list | **15** (CPS shows 16) | Storage-derived, not UI-derived. NeonPlug's `dm32uv/structures.ts` puts the member array at `+0x1A` with the next field at `+0x38` = 30 bytes = 15 x uint16, and both encoder and decoder hard-loop `i < 15`. The CPS shows 16 because it counts the phantom `+0x0F` entry (see below). **Target 15.** |
| Digital contacts | 50,000 | CSV import. (Some test firmware traded the record function for ~150K — varies by build.) |
| RX group lists | 32 | Confirmed in OEM CPS v1.59 (RX groups 1–32). |
| Talk groups per RX group list | 32 | For >32 TGs on one channel, use Group Call Match / promiscuous mode instead. |
| Contacts per... | `?` | Other per-list contact caps unconfirmed. |
| Roaming / channel free / APRS entries | `?` | Not yet enumerated. |

### Notes on behavior
- **Zones are mandatory for display.** Unlike many FM radios, there's no implicit all-channels list; unzoned channels won't show (though they can still sit in a scan list).
- **Scan lists are separate from zones.** Scanning is driven by scan lists, not by "scan this zone." You may build one scan list per zone, but the 15-channel cap can be smaller than a zone's 64.
- **Group Call Match ("promiscuous"/digital monitor):** disabling it lets a DMR channel hear any talk group on its frequency + time slot — the workaround when >32 TGs won't fit an RX group list. Carries a wrong-TG transmit hazard.
- **The 16th scan slot is a phantom, not a bonus member.** Hardware-verified
  2026-08-07 on a DP570UV (upstream NeonPlug PR #156): `+0x0F` is **not** a member
  slot. The OEM CPS moves the first grid member into `+0x0F` on write, and a channel
  sitting there **is neither scanned nor displayed** — it silently vanishes from the
  list. The CPS "Scan Numb" column counts it, which is why CPS-written lists read
  `count = list + 1` and why the cap looks like 16.
  **Leave `+0x0F` zero; `+0x1A` is the entire membership; the `+0x0B` count byte is
  unreliable.** Lists at exactly 15 are fine and are in active use (`AirBand`,
  `FAMILY`, `HAM`, `MARINE`).
  ⚠️ This supersedes an earlier "Current Channel entry registered at index `0`"
  explanation. Both described the same symptom; only `+0x0F` is the mechanism.
  Related decode bug corrected in the same PR: scan-list ID is `0x19 & 0x3F` (low 6
  bits), not bits 5-2 — the old mask cannot address past list 15.

_To fill in the `?` rows: open the OEM CPS, check the max row counts in Scan / RX Group / Zone / Contact tables and drop them here._

## Name Fields & Character Encoding

Name field sizes below are read from the [NeonPlug DM-32UV memory structures](https://github.com/infamy/NeonPlug/blob/main/src/radios/dm32uv/structures.ts) (byte-level codeplug layout). Encoding is **ASCII**, null-terminated, with `0xFF` padding. **Validate max lengths + which characters the OEM CPS actually accepts** against CPS v1.59 — the CPS may enforce a tighter/looser input mask than the raw field allows.

| Field | Name capacity (bytes) | Notes |
|-------|-----------------------|-------|
| Channel name | 16 | 16-byte field, null-terminated (so up to ~16 chars, minus terminator if full). |
| Contact name | 15 | 16-byte field but capped at 15 chars + null terminator. |
| Zone name | 10 | 11-byte field; max 10 chars to leave room for the null terminator, rest `0xFF`-padded. |
| Scan list name | 10 | 11-byte field, null-terminated, max 10 chars. |
| RX group name | 10 | 10-byte field. |
| Key (encryption) name | 10 | 10-byte ASCII field. |
| Radio message text | 128 | ASCII, `0xFF`-terminated (not a name, but same encoding). |

### Encoding notes (from NeonPlug)
- **ASCII only.** NeonPlug decodes/encodes every name with an ASCII TextDecoder/Encoder (`fatal: false`) — non-ASCII input isn't a supported path. UTF-8/emoji in names is unlikely to round-trip.
- **Null-terminated + `0xFF` padding.** Empty/unused entries are marked with leading `0x00` or `0xFF`; a `0xFF` (or `0x00`) first byte = empty slot.
- **CPS may be stricter.** These are the *storage* field sizes, not necessarily what the OEM CPS input boxes allow. Things to validate in CPS v1.59:
  - Actual max characters each name box accepts (does it stop at the byte limits above?).
  - Whether spaces / punctuation / symbols (`- _ / . # *` etc.) are permitted, or if it's alnum-only.
  - Whether lowercase is preserved or force-uppercased.
  - Behavior on over-length paste (truncate vs reject).
