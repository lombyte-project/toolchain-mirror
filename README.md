# Lombyte toolchain mirror

[![Lombyte](https://img.shields.io/badge/Lombyte-Repository-de812f?style=flat-square&logo=github&logoColor=de812f&labelColor=0d1117)](https://github.com/lombyte-project/lombyte)
[![SHA-256](https://img.shields.io/badge/SHA--256-Pinned-c3cbd8?style=flat-square&logo=letsencrypt&logoColor=c3cbd8&labelColor=0d1117)](SHA256SUMS)
[![Contributing](https://img.shields.io/badge/Contributing-Guide-c3cbd8?style=flat-square&logo=github&logoColor=c3cbd8&labelColor=0d1117)](CONTRIBUTING.md)

A copy of every open-source tool and source that
[Lombyte](https://github.com/lombyte-project/lombyte) needs to build
Ratchet & Clank (2002) from C, byte for byte.

Some of these files now live on one old web page or only on archive.org. If an
upstream link disappears, Lombyte's `setup.sh` and compiler build scripts take
the same file from here instead.

> [!NOTE]
> Every file is byte-identical to the one Lombyte pins (same SHA-256).
> No game data and no Sony or SN Systems binaries are stored here.

## The toolchain

The game was not built with one compiler. Each part of the executable came
from a different tool, and Lombyte uses the same split.

| Part of the game | Compiler and assembler | Where we get it |
| --- | --- | --- |
| Sony's SDK libraries | Sony EE-GCC 2.9-ee-991111-01, then GNU `as` (`sdk-as`) | the compiler is Sony's original binary; `sdk-as` is built from source here |
| Game code (boot executable) | EE-GCC 2.9-ee-991111b, then SN's `Ps2EeAs` | the compiler is built from `gnu-ee-binutils-gcc-1.1.tar.gz` and Lombyte's patches |
| Most level code | SN EE-GCC 2.95.2 (ProDG, v2.73a), then `Ps2EeAs` | `cc1` is built from `gcc-2.95.2.tar.gz`, the EE back end of the archive above, and Lombyte's patches; SN's driver and preprocessor are SN's binaries |
| The rest of the level code | the same game compiler as the boot executable | as above |
| Linking, disassembly | `binutils-mips-ps2-decompals` | release binary and source here |
| Comparing our objects with the game | `objdiff` | release binary and source here |
| Splitting the executable, Python tools | splat, spimdisasm and others | `python/` here |

A few boot units still go through SN's original compiler binary. Lombyte's
`configure.py` lists them in `ROUTE_EXCEPTIONS`, and that list only gets
shorter.

Building a 1999 compiler on a modern machine needs a few old helpers:

- **bison 1.28.** The old GCC source needs a bison of its own era (1.2x).
- **A 32-bit host build.** The compilers are built with `gcc -m32`.
- **`-fno-strict-aliasing`.** Without it, a modern host compiler breaks how
  the old code handles negative float constants.

## Why these tools

**Same tools as the original build.** A match means the same bytes. A
compiler that is "close enough" gives different register choices and
instruction order, so the code never matches.

**Built from source when we can.** The game compiler and SN's `cc1` are built
here from GPL source. A binary we cannot read is a black box. A source build
lets us find out why a function does not match and fix it in a small,
documented patch (Lombyte's `patches/`). A patch must copy a change from a
later official GCC release, or model something the real compiler did, shown
by several unrelated functions. It must not make any other unit match worse.

**The source build is as good as SN's own.** Lombyte's SN `cc1` gives the
same assembly as SN's `cc1.exe` for every level unit it was compared on.

**The original binary when it already matches.** The SDK libraries match
Sony's 2.9-ee-991111-01 compiler with no changes. Its exact source was never
published, and we have nothing to fix, so we keep Sony's build.

**SN's assembler for game code.** The game code was assembled by `Ps2EeAs`,
not GNU `as`. It pads loops and fills delay slots in its own way, and the game
shows it. We use version 1.9.25.758 from ProDG 3.01 with two small changes:
no NOPs before `div` (the game has none) and a bigger stack, so large files do
not crash it. It is a Windows program and runs under Wine.

**Community tools for the rest.** decompals binutils and objdiff are the
standard tools of the PS2 decompilation community. They are maintained and
know the R5900 CPU.

## What we tried and dropped

| Alternative | Why it was dropped |
| --- | --- |
| A modern PS2 GCC (PS2DEV, GCC 15) | Optimizes in a different way. Tested on known functions, it was far from every one of them. |
| GCC 3.2.3 (the older PS2DEV recipe) | Newer than the game's compilers. Never built or scored. |
| SN GCC 2.95.3, ProDG 2.2 | No copy could be found. We also decided not to add another compiler line. |
| SN's `cc1.exe` under Wine for all level code | Works, but closed, slow and impossible to fix. The source build gives the same output and can be patched. |
| A second, patched copy of the SDK compiler for two hard functions | Removed. The game was not built that way. The two functions now match with better C and one assembler rule. |
| The older `Ps2EeAs` (1.8.19, ProDG 2.0 archive) | Pads loops differently, so whole files do not match. |
| GNU `as` for the game code | Does not lay out code the way `Ps2EeAs` does. GNU `as` is right only for the SDK libraries. |
| Compiler patches for single functions | Not allowed. A patch has to model a real compiler trait, as above. |

## What is not here

Sony's SDK compiler, SN's compiler package, SN's `Ps2EeAs`, and Sony's SDK
libraries. Their licenses do not allow sharing them, and their exact source
was never published. Lombyte's setup still downloads them from their public
sources. Nothing from the game disc is here either: you need your own copy of
the game to build.

## Files

| File | From | License |
| --- | --- | --- |
| `binutils-mips-ps2-decompals-v0.10.tar.gz` | [decompals/binutils-mips-ps2-decompals](https://github.com/decompals/binutils-mips-ps2-decompals) release v0.10 | GPL-3.0 |
| `binutils-mips-ps2-decompals-0.10-src.tar.xz` | same repository, tag v0.10 (the source of the binaries above) | GPL-3.0, GPL-2.0, LGPL |
| `objdiff-cli-v3.8.0-linux-x86_64` | [encounter/objdiff](https://github.com/encounter/objdiff) release v3.8.0 | MIT or Apache-2.0 |
| `objdiff-3.8.0-src.tar.gz` | same repository, tag v3.8.0 | MIT or Apache-2.0 |
| `gnu-ee-binutils-gcc-1.1.tar.gz` | ps2dev.sourceforge.net, kept by [archive.org](https://web.archive.org/web/20060518234647/http://ps2dev.sourceforge.net:80/downloads/ee/gnu-ee-binutils-gcc-1.1.tar.gz); GCC 2.9-ee-991111b and binutils source | GPL-2.0 |
| `gcc-2.95.2.tar.gz` | [ftp.gnu.org/gnu/gcc](https://ftp.gnu.org/gnu/gcc/) | GPL-2.0 |
| `bison-1.28.tar.gz` | [ftp.gnu.org/gnu/bison](https://ftp.gnu.org/gnu/bison/) | GPL-2.0 |
| `gcc-2.95.2-ee-include.tar.xz` | GCC 2.95.2 headers (`stdarg.h`, `va-mips.h`, ...) from [AngheloAlf/sce_ps2_sdk_24](https://github.com/AngheloAlf/sce_ps2_sdk_24) @ 5c8bdf3, that folder only | GPL-2.0 with the GCC runtime exception |
| `m2c.bundle` | [matt-kempster/m2c](https://github.com/matt-kempster/m2c), git bundle with commit a179fa5 | GPL-3.0 |
| `decomp-permuter.bundle` | [simonlindholm/decomp-permuter](https://github.com/simonlindholm/decomp-permuter), git bundle with commit fb516c4 | MIT |
| `ghidra-emotionengine-reloaded-v2.1.36-ghidra-12.1.2.zip` | [chaoticgd/ghidra-emotionengine-reloaded](https://github.com/chaoticgd/ghidra-emotionengine-reloaded) release v2.1.36 | Apache-2.0 |
| `python/` | PyPI: wheels (Linux x86-64; CPython 3.13 for compiled ones) and source archives of Lombyte's `requirements.txt` | each package's own (MIT, BSD, Apache-2.0, MPL-2.0, public domain), inside its source archive |

Each file keeps its own license. The GPL programs come with their source in
this repository. License texts are in [`licenses/`](licenses); source archives
carry their own. The scripts in `scripts/` are MIT.

## Using a file

Check the files:

```sh
sha256sum -c SHA256SUMS
```

Put a file where Lombyte's setup expects it (`build/downloads/`). A git
bundle clones like a repository:

```sh
git clone m2c.bundle m2c
```
