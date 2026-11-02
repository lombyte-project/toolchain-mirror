# Lombyte toolchain mirror

[Lombyte](https://github.com/lombyte-project/lombyte) rebuilds Ratchet &
Clank's PS2 executable from C, byte for byte. That only works with the exact
old compilers and tools the game needs, and some of them now live on a single
old web page or an archive.org snapshot. This repository keeps a copy of every
open-source piece, so the build keeps working if an upstream link goes away.

Lombyte's `setup.sh` downloads from the original places first and falls back
to this repository. Every file here is byte-identical to the one the setup
pins (same SHA-256). `SHA256SUMS` lists them all.

## The toolchain

The retail game was not built with one compiler. Different parts of the
executable come from different tools, and we use the same split:

| Part of the game | Built with | Where it comes from |
|---|---|---|
| Sony's SDK libraries (the start of the executable) | Sony's EE-GCC 2.9-ee-991111-01, then GNU `as` | Sony's own binary; the assembler is built from source here |
| Most game code | EE-GCC 2.9-ee-991111b, then SN's `Ps2EeAs` assembler | built from `gnu-ee-binutils-gcc-1.1.tar.gz` plus Lombyte's patches |
| Some level code | SN's EE-GCC 2.95.2 (ProDG, v2.73a) | its `cc1` built from `gcc-2.95.2.tar.gz` plus the EE back end and Lombyte's patches |
| Linking, disassembly | `binutils-mips-ps2-decompals` | release binary, source here |
| Comparing our objects with the game | `objdiff` | release binary, source here |
| Splitting the game into files, Python tools | splat, spimdisasm and friends | `python/` |

Old GCC needs old helpers: the 1999 source tree only builds with bison 1.28
(newer bison writes parsers it cannot read), a 32-bit host, and
`-fno-strict-aliasing` (without it a modern host compiler breaks how the old
code handles negative float constants).

## Why these tools

The rule is simple: build each part with the tool that built it in 1999-2002,
or the closest thing we can rebuild from source. A match is only a match if
the bytes come out the same, so "close enough" tools are not enough.

**Rebuild compilers from source instead of running old binaries.** For the
game code and the SN level code we build `cc1` ourselves from GPL source. A
binary we cannot read is a black box; a source build lets us find out *why* a
function does not match and fix the compiler in a small, documented patch
(Lombyte's `patches/`). Each patch either copies a change from a later
official GCC release or matches behavior we can prove from the game itself.
The rebuilt SN `cc1` gives byte-identical output to SN's own `cc1.exe` on
every level unit we tested, so nothing was lost by switching.

**Keep the SDK compiler as Sony's binary.** The SDK libraries match Sony's
2.9-ee-991111-01 compiler as it is, with no changes. Its exact source was never
published, and we do not need to change it, so we use the original.

**Keep SN's assembler, `Ps2EeAs`.** The game code was assembled by it, not by
GNU `as`. It lays out loops and fills delay slots in its own way. We use the
version from ProDG 3.01 (1.9.25.758), with two small binary changes: no NOPs
before `div` (the game has none) and a bigger stack so large files do not crash
it. It is a Windows program, so it runs under Wine.

**Use the decompals binutils and objdiff.** They are the standard tools of
the PS2 decompilation community, maintained, and understand the R5900 CPU.

## Alternatives we tried and dropped

| Alternative | Why it was dropped |
|---|---|
| A modern PS2 GCC (PS2DEV, GCC 15) | Output is far from the game's code. Modern GCC optimizes differently, so almost nothing matches. |
| GCC 3.2.3 (the older PS2DEV recipe) | Newer than the game. Same problem, just smaller. |
| SN's GCC 2.95.3 or ProDG 2.2 builds | No copy could be found anywhere. We also decided not to add a third compiler: two compiler lines are enough to explain the whole game. |
| Running SN's `cc1.exe` under Wine for all level code | Works, but slow, closed and impossible to fix. The source build gives the same output and can be patched. |
| A separate patched copy of the SDK compiler for two hard functions | Removed. Two compilers for one part of the game is not how the game was built. The two functions now match with better C and one assembler rule. |
| The older `Ps2EeAs` from the ProDG 2.0 archive | Pads loops differently, so whole files do not match. |
| GNU `as` for the game code | Does not lay out code the way `Ps2EeAs` does. GNU `as` is right only for the SDK libraries. |
| Patching the compiler for single functions | Not allowed. A patch must model something the real compiler did, shown by several unrelated functions, and must not make any other file match worse. |

## What is not here

Sony's SDK compiler, SN's compiler package and assembler, and Sony's SDK
libraries. Their licenses do not allow us to share them, and their exact
source was never published. The setup still downloads them from their public
sources. Nothing from the game disc is here either: you need your own copy of
the game to build.

## Files and licenses

Each file keeps its own license. The GPL programs come with their source in
this repository. License texts are in `licenses/`; source archives carry their
own.

| File | Origin | License |
|---|---|---|
| `binutils-mips-ps2-decompals-v0.10.tar.gz` | [decompals/binutils-mips-ps2-decompals](https://github.com/decompals/binutils-mips-ps2-decompals) release v0.10 | GPL-3.0 |
| `binutils-mips-ps2-decompals-0.10-src.tar.xz` | same repo, tag v0.10 (source of the binaries above) | GPL-3.0, GPL-2.0, LGPL |
| `objdiff-cli-v3.8.0-linux-x86_64` | [encounter/objdiff](https://github.com/encounter/objdiff) release v3.8.0 | MIT or Apache-2.0 |
| `objdiff-3.8.0-src.tar.gz` | same repo, tag v3.8.0 | MIT or Apache-2.0 |
| `gnu-ee-binutils-gcc-1.1.tar.gz` | ps2dev.sourceforge.net, via [web.archive.org](https://web.archive.org/web/20060518234647/http://ps2dev.sourceforge.net:80/downloads/ee/gnu-ee-binutils-gcc-1.1.tar.gz); GCC 2.9-ee-991111b source | GPL-2.0 |
| `gcc-2.95.2.tar.gz` | [ftp.gnu.org/gnu/gcc](https://ftp.gnu.org/gnu/gcc/) | GPL-2.0 |
| `bison-1.28.tar.gz` | [ftp.gnu.org/gnu/bison](https://ftp.gnu.org/gnu/bison/) | GPL-2.0 |
| `gcc-2.95.2-ee-include.tar.xz` | GCC 2.95.2 headers (`stdarg.h`, `va-mips.h`, ...) from [AngheloAlf/sce_ps2_sdk_24](https://github.com/AngheloAlf/sce_ps2_sdk_24) @ 5c8bdf3, that folder only | GPL-2.0 with the GCC runtime exception |
| `m2c.bundle` | [matt-kempster/m2c](https://github.com/matt-kempster/m2c) (git bundle, has commit a179fa5) | GPL-3.0 |
| `decomp-permuter.bundle` | [simonlindholm/decomp-permuter](https://github.com/simonlindholm/decomp-permuter) (git bundle, has commit fb516c4) | MIT |
| `ghidra-emotionengine-reloaded-v2.1.36-ghidra-12.1.2.zip` | [chaoticgd/ghidra-emotionengine-reloaded](https://github.com/chaoticgd/ghidra-emotionengine-reloaded) release v2.1.36 | Apache-2.0 |
| `python/` | PyPI: wheels and source archives of the packages in Lombyte's `requirements.txt` | each package's own (MIT, BSD, Apache-2.0, MPL-2.0, public domain), inside its source archive |

The scripts in `scripts/` are MIT (their headers say so).

## Using a file

Check it with `sha256sum -c SHA256SUMS`, then put it where the setup expects it
(`build/downloads/` in Lombyte). A git bundle clones like a repository:
`git clone m2c.bundle m2c`.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).
