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
| Channels per scan list | 16 | Confirmed in OEM CPS v1.59 (slots 0–15). Slot `0` = **Current Channel** (not a fixed member). |
| Digital contacts | 50,000 | CSV import. (Some test firmware traded the record function for ~150K — varies by build.) |
| RX group lists | `?` | Number of lists unconfirmed. |
| Talk groups per RX group list | 32 | For >32 TGs on one channel, use Group Call Match / promiscuous mode instead. |
| Contacts per... | `?` | Other per-list contact caps unconfirmed. |
| Roaming / channel free / APRS entries | `?` | Not yet enumerated. |

### Notes on behavior
- **Zones are mandatory for display.** Unlike many FM radios, there's no implicit all-channels list; unzoned channels won't show (though they can still sit in a scan list).
- **Scan lists are separate from zones.** Scanning is driven by scan lists, not by "scan this zone." You may build one scan list per zone, but the 16-channel cap can be smaller than a zone's 64.
- **Group Call Match ("promiscuous"/digital monitor):** disabling it lets a DMR channel hear any talk group on its frequency + time slot — the workaround when >32 TGs won't fit an RX group list. Carries a wrong-TG transmit hazard.
- **Scan list slot 0 = Current Channel.** In CPS v1.59, scan lists hold 16 slots (0–15); adding channel `0` inserts the "Current Channel" placeholder rather than a fixed channel, so the active channel is always scanned alongside the fixed members.

_To fill in the `?` rows: open the OEM CPS, check the max row counts in Scan / RX Group / Zone / Contact tables and drop them here._
