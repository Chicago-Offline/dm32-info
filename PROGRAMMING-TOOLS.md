# DM-32UV Programming Tools

Comparison of programming/CPS options for the Baofeng DM-32UV.

- **OEM CPS** — Baofeng's official Windows CPS ([download area](https://www.baofengradio.com/pages/download))
- **NeonPlug** — web-based CPS, [neonplug.app](https://neonplug.app) · [repo](https://github.com/infamy/NeonPlug)
- **qdmr** — open-source GUI DMR programmer, [dm3mat.de/software/qdmr](https://dm3mat.de/software/qdmr/) · [repo](https://github.com/hmatuschek/qdmr)

| Feature | OEM CPS | NeonPlug | qdmr |
|---------|---------|----------|------|
| Platform | Windows | Browser (Web Serial) | Linux / macOS / Windows |
| Install required | Yes | No (runs in browser; offline single-file available) | Yes |
| Connection | USB | USB (Web Serial); BLE where the radio supports it | USB |
| Read codeplug | ✅ | ✅ | ✅ (on supported radios) |
| Write codeplug | ✅ | ✅ | ✅ (on supported radios) |
| Firmware flashing | ✅ | ❌ | ❌ |
| Codeplug file format | Proprietary | `.neonplug` (zipped JSON) + CHIRP CSV | qdmr text config / YAML |
| CHIRP CSV import/export | ? | ✅ | ? |
| Repeater DB / location import | ? | ✅ (smart import) | ? |
| Bulk table editing | ? | ✅ | ✅ |
| Open source | ❌ | ✅ | ✅ |

> `?` = not yet confirmed for the DM-32UV specifically. NeonPlug feature set per its [README](https://github.com/infamy/NeonPlug). Confirm qdmr's DM-32UV device support against its [supported-devices list](https://dm3mat.de/software/qdmr/) before relying on it — DMR CPS device support varies by model.
