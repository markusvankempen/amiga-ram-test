# Kickstart ROM inventory (Amiberry)

**Last updated:** 2026-05-18  
**Amiberry version:** 8.1.6  
**RAM-TEST version:** V3.11

> *Kickstart ROMs are copyrighted. Use a ROM from your own Amiga hardware or a licensed copy via [Amiga Forever](https://www.amigaforever.com).*

# Kickstart ROM inventory (Amiberry)

Verified on this machine under `~/Documents/Amiberry/ROMs/`. All files match **authentic** checksums and sizes.

## ROM table

Verify your own ROM files using `md5` / `md5sum` and `wc -c`.

| Kickstart version | MD5 | Size |
|-------------------|-----|------|
| **1.2** rev 33.180 (A500) | `85ad74194e87c08904327de1a9443b7a` | 256 KB (262144 bytes) |
| **1.3** rev 34.005 (A500) | `82a21c1890cae844b3df741f2762d48d` | 256 KB (262144 bytes) |
| 1.4 Alpha 18 (developer) | `d2d28b1b35a48e484c6a04e096c2a6de` | 512 KB (524288 bytes) |
| **2.04** rev 37.175 (A500+) | `dc10d7bdd1b6f450773dfb558477c230` | 512 KB (524288 bytes) |
| **3.0** rev 39.106 (A1200) | `b7cc148386aa631136f510cd29e42fc3` | 512 KB (524288 bytes) |
| **3.1** rev 40.063 (A500/A600) | `e40a5dfb3d017ba8779faba30cbd1c8e` | 512 KB (524288 bytes) |
| **3.1** rev 40.068 (A1200 AGA) | `646773759326fbac3b2311fd8c8793ee` | 512 KB (524288 bytes) |

**Important:** 256 KB Kickstarts must be **262144 bytes** on disk. A **524288-byte “doubled”** 1.3 image is **not** bit-identical in the vector region and **breaks** `exec.library` calls (illegal instruction / Guru at `$A880`).

---

## CopyMem / CopyMemFast in `exec.library`

| Function | Role |
|----------|------|
| **CopyMem** | Standard RAM copy; present on all Kickstarts listed above. |
| **CopyMemFast** | Faster copy path used by RAM-TEST V3.11 when filling test patterns. Present on **Kickstart 1.3 and later** (all 512 KB ROMs). |

So: **yes — your ROMs contain proper `exec` memory-copy routines.** The RAM-TEST Gurus we saw were **not** because CopyMem was missing from the ROM.

Typical causes instead:

1. **Wrong ROM file** — doubled/corrupt 1.3 (524288 bytes) or mismatched version vs config.
2. **Amiberry + bogomem** — `CopyMemFast` into trapdoor RAM can still fault even with a good ROM (`cpu_multiplier`, cycle-exact, etc.).
3. **Patched `ram-test-emulator`** — intentionally **NOPs** `CopyMemFast` and uses a byte-fill loop (same idea as `#define EMULATOR` in the SAS reference). The test still runs; it is just slower.

---

## Which ROM for RAM-TEST

| Use | ROM |
|-----|-----|
| **Recommended** | Kickstart **1.3** rev 34.005 — MD5 `82a21c1890cae844b3df741f2762d48d`, 262144 bytes |
| **Also works in principle** | Kickstart 2.04 or 3.1 (A500/A600) — different `exec` layout; change Amiberry config accordingly |
| **Avoid for this project** | Kickstart 1.2 (RAM-TEST expects 1.3-era `exec`), 1.4/3.0/3.1 (A1200) unless you deliberately retest |

Set `kickstart_rom_file=` in `documentation/RAM-TEST_KS13_DF0.uae` to the filename of your Kickstart 1.3 ROM.

---

## Quick verify (macOS)

```bash
# Replace <your-rom-file> with your actual filename:
ROM=~/Documents/Amiberry/ROMs
wc -c "$ROM/<your-rom-file>"
md5 -q "$ROM/<your-rom-file>"
# Expect: 262144 bytes, MD5 82a21c1890cae844b3df741f2762d48d
```

See also [`AMIBERRY-RAM-TEST.txt`](AMIBERRY-RAM-TEST.txt) for Guru codes and memory settings.
