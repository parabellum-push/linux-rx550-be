# !Warning! This repo is vibe-coded.
I am only sharing it since it works on my machine. 
You should probably visit here instead: https://github.com/parabellum-push/rx550-g5

# linux-rx550-be

Kernel patches that let the `amdgpu` driver run an AMD Radeon RX 550 (Polaris 11, GFX8)
on a **big-endian** PowerPC host, a Power Mac G5 (PPC970MP).

The stock driver assumes a little-endian CPU in many places -firmware or VBIOS writes. 
These patches convert those structures at the boundary.

| Patch | Area |
|---|---|
| `0001-amdgpu.patch` | amdgpu core: ring/IB/fence handling, ucode headers, ATOM BIOS, UVD/VCE messages, GFX8, GMC8 |
| `0002-display.patch` | DC (DCE 11.2): VBIOS object tables, HDMI/DP info frames and types |
| `0003-powerplay.patch` | powerplay SMU7/Polaris: PPTable and SMC table byte order |
| `0004-macintosh.patch` | `g5_cpu_temp`: hwmon view of the G5 windfarm temperature sensors |

## Base

kernel.org **linux 7.2.9**, plus Debian's
`radeon-amdgpu-firmware-is-required-for-drm-and-kms-on-r600-onward.patch` (included here,
unchanged, from Debian `linux_7.2.9-1.debian.tar.xz`). Apply that first, then `0001`–`0004`:

```sh
tar xf linux-7.2.9.tar.xz && cd linux-7.2.9
patch -p1 < ../patches/debian-radeon-amdgpu-firmware-is-required-for-drm-and-kms-on-r600-onward.patch
for p in ../patches/000*.patch; do patch -p1 < "$p"; done
cp ../config/config-7.2.9+deb14-powerpc64-rx550g5 .config
make olddefconfig && make -j"$(nproc)" bindeb-pkg LOCALVERSION=-rx550g5
```

The binary package (`linux-image-7.2.9+deb14-powerpc64-rx550g5`) is in the
releases of [rx550-g5](https://github.com/parabellum-push/rx550-g5), together with the Mesa
packages it is meant for.

## Status

Tested on one Power Mac G5 with one RX 550: boot, display (up to 2560x1440@144), GL/Vulkan/OpenCL
via the patched Mesa, VA-API decode and VCE encode. Other GFX8 boards (RX 460-590) share the code but
were not tested.


## License

GPL-2.0. See https://www.kernel.org/doc/html/latest/process/license-rules.html
