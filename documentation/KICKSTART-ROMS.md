# Kickstart ROM inventory (Amiberry)

**Last updated:** 2026-05-18  
**Amiberry version:** 8.1.6  
**RAM-TEST version:** V3.11

> ⚠️ **Legal notice:** Kickstart ROMs are copyrighted software owned by **Cloanto**.
> You must use a ROM you own legally — dumped from your own Amiga hardware, or purchased
> via **[Amiga Forever](https://www.amigaforever.com)** (the officially licensed ROM pack).
> Do not download ROMs from third-party sites. They are not abandonware.

# Kickstart ROM inventory (Amiberry)

Verified on this machine under `~/Documents/Amiberry/ROMs/`. All files match **authentic** checksums and sizes.

## ROM table

| File | MD5 | Size | Notes |
|------|-----|------|--------|
| `kick12.rom` | `85ad74194e87c08904327de1a9443b7a` | 256 KB (262144) | Kickstart **1.2** rev 33.180 (A500) |
| `kick13.rom` | `82a21c1890cae844b3df741f2762d48d` | 256 KB | Kickstart **1.3** rev 34.005 (A500) |
| `kick34005.A500.rom` | `82a21c1890cae844b3df741f2762d48d` | 256 KB | **Same file as `kick13.rom`** (automation alias) |
| `kick14.rom` | `d2d28b1b35a48e484c6a04e096c2a6de` | 512 KB | Kickstart 1.4 Alpha 18 (developer) |
| `kick204.rom` | `dc10d7bdd1b6f450773dfb558477c230` | 512 KB | Kickstart **2.04** rev 37.175 (A500+) |
| `kick30.rom` | `b7cc148386aa631136f510cd29e42fc3` | 512 KB | Kickstart **3.0** rev 39.106 (A1200) |
| `kick31_a500.rom` | `e40a5dfb3d017ba8779faba30cbd1c8e` | 512 KB | Kickstart **3.1** rev 40.063 (A500/A600) |
| `kick31_a1200.rom` | `646773759326fbac3b2311fd8c8793ee` | 512 KB | Kickstart **3.1** rev 40.068 (A1200 AGA) |

**Important:** 256 KB Kickstarts must be **262144 bytes** on disk. A **524288-byte “doubled”** 1.3 image is **not** bit-identical in the vector region and **breaks** `exec.library` calls (illegal instruction / Guru at `$A880`).

---

## CopyMem / CopyMemFast in `exec.library`

| Function | Role |
|----------|------|
| **CopyMem** | Standard RAM copy; present on all Kickstarts listed above. |
| **CopyMemFast** | Faster copy path used by RAM-TEST V3.11 when filling test patterns. Present on **Kickstart 1.3 and later** in this set (`kick13.rom` / `kick34005.A500.rom` and all 512 KB ROMs). |

So: **yes — your ROMs contain proper `exec` memory-copy routines.** The RAM-TEST Gurus we saw were **not** because CopyMem was missing from the ROM.

Typical causes instead:

1. **Wrong ROM file** — doubled/corrupt 1.3 (524288 bytes) or mismatched version vs config.
2. **Amiberry + bogomem** — `CopyMemFast` into trapdoor RAM can still fault even with a good ROM (`cpu_multiplier`, cycle-exact, etc.).
3. **Patched `ram-test-emulator`** — intentionally **NOPs** `CopyMemFast` and uses a byte-fill loop (same idea as `#define EMULATOR` in the SAS reference). The test still runs; it is just slower.

---

## Which ROM for RAM-TEST

| Use | ROM |
|-----|-----|
| **Recommended** | `kick34005.A500.rom` or `kick13.rom` (identical MD5) |
| **Also works in principle** | `kick204.rom`, `kick31_a500.rom` — different `exec` layout; use only if you change the Amiberry config and accept different timing/OS behaviour. |
| **Avoid for this project** | `kick12.rom` (1.2 — RAM-TEST expects 1.3-era `exec`), `kick14`/`kick30`/`kick31_a1200` unless you deliberately retest |

Preset `documentation/RAM-TEST_KS13_DF0.uae` sets:

```text
kickstart_rom_file=kick34005.A500.rom
```

---

## Quick verify (macOS)

```bash
ROM=~/Documents/Amiberry/ROMs
wc -c "$ROM/kick13.rom" "$ROM/kick34005.A500.rom"
md5 -q "$ROM/kick13.rom" "$ROM/kick34005.A500.rom"
# Expect: 262144 each, same MD5 82a21c1890cae844b3df741f2762d48d
```

See also [`AMIBERRY-RAM-TEST.txt`](AMIBERRY-RAM-TEST.txt) for Guru codes and memory settings.
