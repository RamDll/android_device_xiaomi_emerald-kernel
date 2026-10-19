# soc_temp_lvts.ko source for emerald

MediaTek LVTS thermal driver (the CPU / GPU / SoC sensors of mt6789), built out of tree for the stock GKI
kernel in this repo (6.12.30, ci.android.com build 13847919, kernel/common 6e872b4863d6). Not used by the
build: vendor_dlkm still ships the stock module.

| File | Source |
|---|---|
| `soc_temp_lvts.c`, `soc_temp_lvts.h` | [oppo-source/android_kernel_modules_and_devicetree_oppo_mt6993](https://github.com/oppo-source/android_kernel_modules_and_devicetree_oppo_mt6993) 4d505a4292dab3176a45fec66dc85debc362e24c, `kernel/kernel_device_modules-6.12/drivers/thermal/mediatek/` (MediaTek, GPL-2.0). Closest of four candidate trees to the stock OS3.0.305.0 module: 89 of its 92 strings, the 3 missing are Xiaomi debug prints |
| `thermal_interface.h` | [zhaoqing412/android_kernel_modules_xiaomi_mt6895-6.12](https://github.com/zhaoqing412/android_kernel_modules_xiaomi_mt6895-6.12) 7c3d5677804b46fda6838537f95bf3ce2d5953ad, `kernel_device_modules-6.12/drivers/thermal/mediatek/` |
| `mtk-include/linux/soc/mediatek/mtk_sip_svc.h` | same tree, `kernel_device_modules-6.12/include/linux/soc/mediatek/`. Force-included by Kbuild: it has the same include guard as the GKI header, which lacks `MTK_SIP_KERNEL_THROTTLE_CONTROL` |
| `Module.symvers.ti` | CRCs of the four `thermal_interface` exports as the stock module imports them (`ko_versions.py`) |
| `Kbuild`, `ko_versions.py`, `cfi_hashes.py` | ours |

## Build

1. Kernel tree: `kernel/common` at 6e872b4863d6dd892490c32d973db1a9df8d3710. `.config` = the phone's
   `/proc/config.gz`, then `make olddefconfig modules_prepare` with `ARCH=arm64 LLVM=1`, clang r547379 and
   pahole (`prebuilts/kernel-build-tools/linux-x86/bin`) in `PATH`. Without pahole BTF is turned off and
   `struct module` changes layout; without rustc a `gki_defconfig` build drops
   `CONFIG_CFI_ICALL_NORMALIZE_INTEGERS` and every kCFI type hash differs. An empty
   `protected_module_names_list` keeps modpost quiet.
2. `Module.symvers.all` = `vmlinux.symvers` from ci.android.com build 13847919 (`kernel_aarch64`) +
   `Module.symvers.ti`. Never use CRCs computed locally: gendwarfksyms with a newer clang does not match the
   running kernel.
3. Build:

       make -C <out> ARCH=arm64 LLVM=1 M=$PWD KBUILD_EXTRA_SYMBOLS=$PWD/Module.symvers.all modules

## Checks before loading it on the phone

A wrong kCFI hash panics the kernel on `insmod`, so compare with the stock module first:

- `ko_versions.py <llvm-objcopy> <ko>`: the CRC of every import equals the stock module's; new imports
  come from the official `vmlinux.symvers`.
- `cfi_hashes.py <llvm-readelf> <ko>`: the type hashes of the functions both modules have are equal;
  `init_module` is `0x6fbb3035`.
- `.gnu.linkonce.this_module`: size 0x640, `init_module` relocation at 0x188 and `cleanup_module` at
  0x5f8, as in the stock module.
