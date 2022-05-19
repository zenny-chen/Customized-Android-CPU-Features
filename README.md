# Customized Android CPU Features
Customized Android CPU Features, especially extend ARMv8 supplementary features

To compile with ARMv8.4 plus AES and SHA3 instruction set, use `-march=armv8.4a+aes+sha3`.

In Android Studio NDK CMakeLists.txt, you may specify the assembly compiling option to use the instructions via assembly code:

```cmake
set(CMAKE_ASM_FLAGS "-march=armv8.4a+aes+sha3" ${CMAKE_ASM_FLAGS}")
```

In addition in Windows 10, `cpu-features.h` is located in `%USERPROFILE%/AppData/Local/Android/Sdk/ndk/<ndk version>/sources/android/cpufeatures/`.

#### Relevant information

- [ARMv8 - ARM](https://en.wikichip.org/wiki/arm/armv8)
- [arm/armv8.1](https://en.wikichip.org/wiki/arm/armv8.1)
- [https://gcc.gnu.org/onlinedocs/gcc/AArch64-Options.html](https://gcc.gnu.org/onlinedocs/gcc/AArch64-Options.html)
- [Memory tagging extension (MTE, MemTag, ARMv8.5-MemTag)](https://en.wikichip.org/wiki/arm/mte)
- [Runtime detection of CPU features on an ARMv8-A CPU](https://community.arm.com/developer/tools-software/oss-platforms/b/android-blog/posts/runtime-detection-of-cpu-features-on-an-armv8-a-cpu)
- [Bringing Armv8.2 Instructions to Android Runtime](https://community.arm.com/developer/tools-software/oss-platforms/b/android-blog/posts/bringing-armv8-2-instructions-to-android-runtime)
- [package cpu](https://www.godoc.org/golang.org/x/sys/cpu) （There's an error here: `ASIMDDP` does not mean Double-Precision. It means Dot Production, i.e. has_dotprod_feature.）
- `STLUR`, `STLURB` and `STLURH` require **ARMv8.4a** architecture.

<br />

## Android或Linux系统下获取ARM64架构处理器特性的demo

```c
#ifdef __aarch64__
#include <sys/auxv.h>
#include <asm/hwcap.h>

enum ARM64HostFeature
{
    ARM64HostFeatures_AES = 1 << 0,
    ARM64HostFeatures_CRC32 = 1 << 1,
    ARM64HostFeatures_FP_HALF = 1 << 2,
    ARM64HostFeatures_ATOMICS = 1 << 3
};

unsigned g_hostFeatures = 0;

void GatherARM64HostFeatures(void)
{
    unsigned long long hwcaps = getauxval(AT_HWCAP);
    g_hostFeatures |= (hwcaps & HWCAP_AES) != 0 ? ARM64HostFeatures_AES : 0U;
    g_hostFeatures |= (hwcaps & HWCAP_CRC32) != 0 ? ARM64HostFeatures_CRC32 : 0U;
    g_hostFeatures |= (hwcaps & HWCAP_FPHP) != 0 ? ARM64HostFeatures_FP_HALF : 0U;
    g_hostFeatures |= (hwcaps & HWCAP_ATOMICS) != 0 ? ARM64HostFeatures_ATOMICS : 0U;
}

#endif
```

以上纯C代码在Android或Linux下，无论是通过GCC还是Clang均可使用。

