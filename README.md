# am-toolchains-aarch64

**aarch64-host** rebuilds of the Broadcom HND 5.04behnd.4916 crosstools used by
[RMerl/am-toolchains](https://github.com/RMerl/am-toolchains) — same versions, same tuning,
regenerated from upstream sources with Buildroot 2021.02.4 (the same generator the shipped
x86_64 toolchains came from). Proposed for upstream inclusion in
[RMerl/am-toolchains#6](https://github.com/RMerl/am-toolchains/issues/6).

- `crosstools-arm_softfp-gcc-10.3-linux-4.19-glibc-2.32-binutils-2.36.1` — target tuning `cortex-a9 / vfpv3 / softfp`
- `crosstools-aarch64-gcc-10.3-linux-4.19-glibc-2.32-binutils-2.36.1` — target tuning `cortex-a53`

**Verified:** full asuswrt-merlin firmware builds on an ARM64 host (NVIDIA DGX Spark, 4K-page kernel) —
GT-BE98 + GT-BE98_PRO on gnuton `DEV_3006.102.7_2` and GT-BE98_PRO on RMerl `main` (102.8_3),
flashable pkgtb images produced.

## Usage

```sh
N=crosstools-arm_softfp-gcc-10.3-linux-4.19-glibc-2.32-binutils-2.36.1
mkdir -p /opt/toolchains/$N
zstd -dc ${N}_aarch64-host_sdk.tar.zst | tar x -C /opt/toolchains/$N --strip-components=1
ln -s . /opt/toolchains/$N/usr          # match vendor layout: both X/bin and X/usr/bin resolve
(cd /opt/toolchains/$N && ./relocate-sdk.sh)
```

Host binaries are built on Ubuntu 20.04 (glibc 2.31) — they run on that or anything newer —
and are linked at the standard aarch64 64K max-page-size, so they load on 4K/16K/64K-page kernels alike.

## Known deltas vs the vendor x86_64 toolchains

The vendor sysroots carry three extras a stock Buildroot toolchain lacks, all needed by the firmware userland build:
- `libtirpc` in the sysroot (add `BR2_PACKAGE_LIBTIRPC=y`, or copy from the x86 originals — target libs are host-independent)
- glibc's obsolete-NSL `libnsl` in the sysroot (same options)
- `libexpat` in the toolchain's host `lib/` (referenced by a legacy install rule; content never reaches the image)

## Complete corresponding source

The `corresponding-sources_*.tar.zst` release asset contains the Buildroot 2021.02.4 tarball plus the
complete `dl/` cache (gcc/glibc/binutils/... source archives) — everything needed to reproduce these
binaries offline with the included defconfigs. Build logs are published alongside.

## Provenance

Engineered by Claude (Anthropic's AI assistant) under the direction and authorization of leonpano2006,
who verified all builds and images on real hardware and is the accountable maintainer of this repo.
