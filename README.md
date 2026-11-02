# Lombyte toolchain mirror

Copies of the open-source tools and sources that the
[Lombyte](https://github.com/lombyte-project/lombyte) setup downloads, kept
here so the build keeps working if an upstream link goes away. Every file is
byte-identical to the upstream file the setup pins (same SHA-256), or a git
bundle / archive of the pinned commit. `SHA256SUMS` lists them all.

Each file keeps its own license. The GPL binaries ship with their source in
this repo. License texts are in `licenses/`; source archives carry their own.

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

Not here: the Sony SDK compiler, the SN Systems compiler and assembler, and the
Sony SDK libraries. Their licenses do not allow redistribution, so the setup
still downloads them from their public sources.

Use a file: `sha256sum -c SHA256SUMS`, then put it where the setup expects it
(`build/downloads/` in Lombyte). A git bundle clones like a repo:
`git clone m2c.bundle m2c`.
