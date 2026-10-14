# Linux 4.4 kernel for the OnePlus 3 / 3T

The OnePlus 3 (15801) and 3T (15811) port on top of the LineageOS
Xiaomi MSM8996 kernel (`lineage-22.2`, Linux 4.4.302). The goal is
LineageOS 22.2 (Android 15) on the OnePlus 3.

## Branches

| Branch | Base | Use |
|---|---|---|
| `op3-22.2` | LineageOS `android_kernel_xiaomi_msm8996` lineage-22.2 | LineageOS 22.2 (work in progress) |
| `op3-18.1` | tangalbert919 `staging/lineage-20` | LineageOS 18.1, stable |

## Status

Tested on the OnePlus 3 with LineageOS 18.1, SELinux enforcing.

Working: boot, display, touch, fingerprint, alert slider, vibration,
notification LED, sensors, camera (photo and video), WiFi, Bluetooth,
LTE registration, USB/ADB, charging with the OP_3000mah battery profile
and charge termination, cpusets, BFQ.

Not tested yet on this branch: audio, calls, NFC, Dash charging, deep
sleep, the OnePlus 3T.

LineageOS 22.2 userspace has not been brought up yet.

## Building

The config is the Xiaomi base plus a OnePlus 3 fragment:

```
make ARCH=arm64 O=out vendor/xiaomi/mi8996_defconfig vendor/oneplus/oneplus3.config
make ARCH=arm64 O=out CC=clang CLANG_TRIPLE=aarch64-linux-gnu- \
     CROSS_COMPILE=aarch64-linux-android- \
     CROSS_COMPILE_ARM32=arm-linux-androideabi-
```

The output is `out/arch/arm64/boot/Image.gz-dtb`.

The OnePlus 3 bootloader puts the device tree at `0x82000000`, so the
kernel image including bss (the `image_size` field of the arm64 Image
header) has to stay below 31.5 MB. The kernel is therefore built with
`CONFIG_CC_OPTIMIZE_FOR_SIZE`. A kernel that is too large hangs at the
boot logo without any log.

## Notes on the port

- `CONFIG_MACH_ONEPLUS_MSM8996` selects the OnePlus device trees and
  drivers; the Xiaomi boards keep working with `CONFIG_MACH_XIAOMI_*`.
- The camera stack (`camera_v2` and its UAPI headers) comes from the
  `op3-18.1` branch, since the OnePlus camera blobs need the CAF
  interface that the Xiaomi camera stack changed.
- The Xiaomi CPR voltage tables are reverted for the OnePlus 3.

## Credits

- dianlujitao for the original 4.4 port of the OnePlus 3
- tangalbert919 for rebasing and continuing that port
- The LineageOS team and the Xiaomi MSM8996 maintainers for the
  kernel this branch is based on
- OnePlus for the kernel sources of the OnePlus 3
- Qualcomm / CodeAurora for the MSM kernel
- Sultanxda for the tri-state-key driver
- John Dias for the FG sanity check fix
- Everyone else in the git history
