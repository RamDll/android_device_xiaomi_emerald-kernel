# emerald-kernel (clean-1)

Prebuilt kernel for Xiaomi emerald, taken unmodified from stock HyperOS 3 Global `OS3.0.305.0.WNFMIXM`.

| File | Source |
|---|---|
| `Image.lz4` | stock `boot.img` kernel; byte-identical to Google GKI `6.12.30-android16-5-g6e872b4863d6`, ci.android.com build 13847919 `kernel_aarch64/Image.lz4` (md5 bf551ed6a18163c862bb278c57d98735) |
| `kernel-uapi-headers.tar.gz` | ci.android.com build 13847919 `kernel_aarch64/kernel-uapi-headers.tar.gz` |
| `dtb/mt6789.dtb` | stock `vendor_boot.img` dtb |
| `dtbo.img` | stock `dtbo.img` |
| `vendor_ramdisk/` | stock `vendor_boot.img` vendor_ramdisk00 `lib/modules` (*.ko, modules.load, modules.load.recovery) |

vendor_dlkm, system_dlkm and odm_dlkm are not here: the device tree ships the stock partition images
unchanged (`BOARD_PREBUILT_*IMAGE`), so their modules come with them. Note that zram/zsmalloc exist
in both vendor_ramdisk and system_dlkm with different contents (as on stock).

Stock vendor_boot bootconfig (for reference): `kernel.rcu_nocbs=all kernel.rcutree.enable_rcu_lazy=1
kernel.rcupdate.rcu_cpu_stall_cputime=1`; vendor cmdline `bootopt=64S3,32N2,64N2 bootconfig`.
