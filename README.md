# emerald-kernel (clean-1)

Prebuilt kernel for Xiaomi emerald, taken unmodified from stock HyperOS 3 Global `OS3.0.305.0.WNFMIXM`.

| File | Source |
|---|---|
| `Image.lz4` | stock `boot.img` kernel; byte-identical to Google GKI `6.12.30-android16-5-g6e872b4863d6`, ci.android.com build 13847919 `kernel_aarch64/Image.lz4` (md5 bf551ed6a18163c862bb278c57d98735) |
| `kernel-uapi-headers.tar.gz` | ci.android.com build 13847919 `kernel_aarch64/kernel-uapi-headers.tar.gz` |
| `dtb/mt6789.dtb` | stock `vendor_boot.img` dtb |
| `dtbo.img` | stock `dtbo.img` |
| `vendor_ramdisk/` | stock `vendor_boot.img` vendor_ramdisk00 `lib/modules` (*.ko, modules.load, modules.load.recovery) |
| `vendor_ramdisk/{xiaomi,switch_class,ft3683g,gt9916}.ko` | stock `vendor_dlkm.img` `lib/modules`, unmodified; appended to modules.load.recovery only (touchscreen in recovery: Focaltech ft3683g, Goodix gt9916 and their two vendor_dlkm dependencies) |

vendor_dlkm, system_dlkm and odm_dlkm are not here: the device tree ships the stock partition images
(`BOARD_PREBUILT_*IMAGE`; vendor_dlkm with one module replaced, see below), so their modules come with them. Note that zram/zsmalloc exist
in both vendor_ramdisk and system_dlkm with different contents (as on stock).

`modules/soc_temp_lvts/` is the source of the MediaTek LVTS thermal driver, built out of tree for this
kernel (see its README). `vendor_dlkm/soc_temp_lvts.ko` is that module as built (clang r547379, then
`llvm-strip --strip-debug` like the stock modules); device/xiaomi/emerald patch-images.py puts it into
vendor_dlkm.img in place of the stock one.

Stock vendor_boot bootconfig (for reference): `kernel.rcu_nocbs=all kernel.rcutree.enable_rcu_lazy=1
kernel.rcupdate.rcu_cpu_stall_cputime=1`; vendor cmdline `bootopt=64S3,32N2,64N2 bootconfig`.
