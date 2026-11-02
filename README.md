<p align="center">
  <img src="assets/lombyte-logo.png" alt="Lombyte" width="135">
</p>

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/title-dark.svg">
    <img src="assets/title-light.svg" alt="Lombyte Project, Toolchain Mirror" height="62">
  </picture>
</p>

<p align="center">
  <a href="https://github.com/lombyte-project/lombyte"><img src="https://img.shields.io/badge/Lombyte-Repository-de812f?style=flat-square&logo=github&logoColor=de812f&labelColor=0d1117" alt="Lombyte repository"></a>
  <a href="SHA256SUMS"><img src="https://img.shields.io/badge/SHA--256-Pinned-c3cbd8?style=flat-square&logo=letsencrypt&logoColor=c3cbd8&labelColor=0d1117" alt="SHA-256 pinned"></a>
  <a href="CONTRIBUTING.md"><img src="https://img.shields.io/badge/Contributing-Guide-c3cbd8?style=flat-square&logo=github&logoColor=c3cbd8&labelColor=0d1117" alt="Contributing guide"></a>
</p>
<p align="center">
    A copy of every open-source tool <a href="https://github.com/lombyte-project/lombyte">Lombyte</a> needs to build.<br>
    If an upstream link disappears, the setup takes the file from here.
</p>

> [!NOTE]
> Every file is byte-identical to the one Lombyte's `setup.sh` pins.<br>
> No game data and no Sony or SN Systems binaries are stored here.

<h3>The toolchain</h3>

The game was built with several old compilers, one per part of the code. We use the same split.

| Part of the game | Compiler | How we get it |
| ---------------- | -------- | ------------- |
| Sony SDK libraries | Sony EE-GCC 2.9-991111-01 | Sony's original binary |
| Game code | EE-GCC 2.9-991111b + SN `Ps2EeAs` | compiler built from source here |
| Some level code | SN EE-GCC 2.95.2 | compiler built from source here |
| Linking, disassembly | binutils (decompals) | release + source here |
| Comparing with the game | objdiff | release + source here |

<h3>Why these tools</h3>

- **Same tools as 2002.** A match means the same bytes, so "close enough" compilers do not work.
- **Built from source when we can.** We can read and fix a compiler we build. A closed binary is a black box.
- **Original binary when it already matches.** The SDK compiler needs no changes, so we keep Sony's build.
- **SN's assembler for game code.** It lays out loops and delay slots its own way, and the game shows it.
- **Old helpers for old GCC.** The 1999 source only builds with bison 1.28 on a 32-bit host.

<h3>What we tried and dropped</h3>

| Alternative | Why not |
| ----------- | ------- |
| Modern PS2 GCC (PS2DEV, GCC 15) | Optimizes differently, almost nothing matches |
| GCC 3.2.3 | Still too new for this game |
| SN GCC 2.95.3, ProDG 2.2 | No copy exists online; two compilers already explain the whole game |
| SN `cc1.exe` under Wine | Closed and slow; our source build gives the same output |
| A second, patched SDK compiler | Not how the game was built; fixed in C and one assembler rule |
| Older `Ps2EeAs` (ProDG 2.0) | Pads loops differently |
| GNU `as` for game code | Lays out code differently than SN's assembler |

<h3>Files</h3>

<details>
<summary>Every file, where it comes from, and its license</summary>

| File | From | License |
| ---- | ---- | ------- |
| `binutils-mips-ps2-decompals-v0.10.tar.gz` + `-src.tar.xz` | [decompals/binutils-mips-ps2-decompals](https://github.com/decompals/binutils-mips-ps2-decompals) v0.10 | GPL-3.0 |
| `objdiff-cli-v3.8.0-linux-x86_64` + `objdiff-3.8.0-src.tar.gz` | [encounter/objdiff](https://github.com/encounter/objdiff) v3.8.0 | MIT or Apache-2.0 |
| `gnu-ee-binutils-gcc-1.1.tar.gz` | ps2dev.sourceforge.net via [archive.org](https://web.archive.org/web/20060518234647/http://ps2dev.sourceforge.net:80/downloads/ee/gnu-ee-binutils-gcc-1.1.tar.gz) | GPL-2.0 |
| `gcc-2.95.2.tar.gz` | [ftp.gnu.org](https://ftp.gnu.org/gnu/gcc/) | GPL-2.0 |
| `bison-1.28.tar.gz` | [ftp.gnu.org](https://ftp.gnu.org/gnu/bison/) | GPL-2.0 |
| `gcc-2.95.2-ee-include.tar.xz` | GCC headers from [sce_ps2_sdk_24](https://github.com/AngheloAlf/sce_ps2_sdk_24) @ 5c8bdf3 | GPL-2.0 + GCC exception |
| `m2c.bundle` | [matt-kempster/m2c](https://github.com/matt-kempster/m2c) | GPL-3.0 |
| `decomp-permuter.bundle` | [simonlindholm/decomp-permuter](https://github.com/simonlindholm/decomp-permuter) | MIT |
| `ghidra-emotionengine-reloaded-*.zip` | [chaoticgd/ghidra-emotionengine-reloaded](https://github.com/chaoticgd/ghidra-emotionengine-reloaded) v2.1.36 | Apache-2.0 |
| `python/` | PyPI packages from Lombyte's `requirements.txt` | each package's own |

License texts are in [`licenses/`](licenses). Scripts in `scripts/` are MIT.

</details>

Check a file with `sha256sum -c SHA256SUMS`. A `.bundle` clones like a repo: `git clone m2c.bundle m2c`.
