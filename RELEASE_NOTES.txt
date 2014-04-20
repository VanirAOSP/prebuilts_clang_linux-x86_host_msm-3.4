This is the release of Snapdragon LLVM ARM C/C++ Compiler version 3.4 for
Android NDK.  This is a cross compiler built to run on x86 Ubuntu Linux 64/32-bit
(version 10.04) and Windows platforms. This Snapdragon LLVM ARM Compiler will
generate code only for ARM targets and will not generate code for any of the
other targets supported by llvm.org LLVM.

The version 3.4 of Snapdragon LLVM ARM Compiler is based off the llvm.org 3.4
release. The Snapdragon LLVM ARM Compiler includes proprietary features,
optimizations and bug fixes that are not available in the llvm.org 3.4 version.
It uses the integrated assembler for assembling; the other toolchain elements
such as the linker are provided by the Android NDK.

Please post all questions about this release on
http://developer.qualcomm.com/llvm-forum

The following are some of the key features in the 3.4 version compared to the
3.3 version

1. Support for code generation for ARMv8 architecture: This version of
Snapdragon LLVM ARM Compiler supports code generation for both AArch32 (32-bit)
and AArch64 (64-bit) modes. This includes 
  - full support for generation of all new instructions in AArch32 (compared to
    ARMv7)
  - Support for generating AArch64 scalar instructions (including floating
    point) 
  - Support for AArch64 Neon intrinsics
  - Support for assembling all instructions in AArch64 ISA
  - Code generation support for all scalar and many Neon instructions,
    including support for many Neon intrinsics. Note that this release of
    Snapdragon compiler supports more ARMv8 Neon intrinsics compared to the
    llvm.org 3.4 release
  - Several improvements and bug fixes in ARMv8 code generation compared to
    llvm.org 3.4 release

This is the first release of Snapdragon LLVM compiler with full functional
support for ARMv8 but does not include performance optimizations (such as
tuning for Cortex-a53 and Cortex-a57). Optimizations for ARMv8 cores are
expected in the next release of Snapdragon LLVM compiler.

2. Address sanitizer: This release introduces support for Address-Sanitizer, a
security tool for detecting common memory errors in an application such as
out-of-bounds, use-after-free and double-free. Please refer to section 3.9 of
the Snapdragon LLVM ARM Compiler document for more details

3. Integrated assembler with support for assembling hand-coded assembly files:
Compared to the previous release where integrated assembler was limited to code
generation for C/C++ source files, this release provides support for assembling
hand-coded assembly files that follow GNU assembler (GAS) syntax.

4. Improvements to Auto-vectorizer: The Auto-Vectorization feature has been
further strengthened to cover more loop idioms and patterns.


LLVM's Support for GCC command line options:

Most of the commonly used GCC options are supported by LLVM as well. A detailed
list of options supported is listed in chapter 4 of the Snapdragon LLVM ARM
Compiler user guide under the docs directory.


Basic Usage & Examples:

clang -O3 -o foo foo.c
clang++ -Os -mthumb -o bar bar.cpp

Options:

To find out the most common options, please pass -help option to the clang
driver. This will report the Snapdragon ARM specific options as well as general
ARM options first before listing target independent options available in
Snapdragon LLVM ARM Compiler.

Compiler options for best performance:

We recommend using the "-Ofast -mcpu=krait2" options for best performance of
your application on Krait cores. For non-Krait cores, we recommend using the
"-Ofast" option. If you would like to avoid the generation of Krait specific
instructions (hardware divide, fused-mac) to enable running on a variety of
ARMv7 cores, but still optimize for best performance on Krait cores, we
recommend the "-Ofast -mtune=krait2 -march=armv7-a" option list.

The following link provides guidance on which Qualcomm processors include Krait
or Cortex-A7 cores: 

http://www.qualcomm.com/snapdragon/processors/800-600-400-200/specs 

The -Ofast option enables several high performance optimizations such as
Auto-vectorizer. Please refer to section 3.4.18 of the Snapdragon LLVM ARM
Compiler guide for more details.

Compiler options for smallest code-size:

We recommending using the "-Osize -mthumb" options to generate the most compact
code with the Snapdragon LLVM 3.4 compiler. Please refer to section 3.4.18 for
more details on the -Osize optimization level

To enable these recommended flags for your build, please add them to the
LOCAL_CFLAGS variable in your project's Android.mk file(s).


Compiling for AArch64:

To compile for AArch64 (ARMv8 64-bit mode), please pass 

-target aarch64-linux-gnueabi

option to clang.

Compiling for AArch32:

To compile for AArch32 (the ARMv8 32-bit mode fully backward compatible to
ARMv7 mode), please pass

-target armv8-linux-gnueabi

option to clang. Note that even though AArch32 ISA is fully backward compatible
with ARMv7 ISA, there are new instructions in AArch32 ISA compared to ARMv7
ISA. This means that AArch32 executables cannot be executed on ARMv7 cores such
as Krait.

NOTE: While ARMv8 cores are increasingly becoming popular, current releases of
Android NDK (r9c and older) do not have support for ARMv8 compilation; there is
no sysroot or binutils toolchain that is available in Android NDK for ARMv8.
We recommend using popular alternatives such as the Linaro ARMv8 toolchain for
experimentation with ARMv8 code generation.

Using Snapdragon LLVM ARM Compiler 3.4 with Android NDK:

Snapdragon LLVM ARM Compiler 3.4 can be used as a drop-in replacement
for LLVM shipped as part of the Android NDK.

1. Snapdragon LLVM ARM Compiler 3.4 has been verified to work with Android NDK
versions r8e and later for Windows and Linux (32 and 64 bit).

2. On Windows, it is assumed that the user has Cygwin setup as required by the
Android NDK instructions. Also on Windows, Microsoft Visual C++ 2010
Redistributable Package (x86) is required in order to use Snapdragon LLVM ARM
Compiler 3.4. If your system does not already have it you can download and
install it from the following link:
http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=5555

3. On Windows and Linux it is recommended that you install/extract the Android
NDK under a directory which *does not* contain spaces, like:

C:\android-ndk-r9
/local/mnt/workspace/android-ndk-r9

This directory is referred to as <NDK_ROOT> in this README.

4. Unzip Snapdragon-llvm-3.4-compiler-windows.zip or untar
Snapdragon-llvm-3.4-compiler-linux64.tar.gz and
Snapdragon-llvm-3.4-compiler-linux32.tar.gz under <NDK_ROOT>/toolchains.

5. By default, it is assumed that the user has Android NDK r9 or later version
and hence will use arm-linux-androideabi-4.8 for linking. If you want to use
Android NDK r8e then you must set the variable NDK_VERSION=r8e (as shown below)
and arm-linux-androideabi-4.7 would be used for linking.

6. Invoke your compilation line as follows:

NDK_TOOLCHAIN_VERSION=snapdragonclang3.4 ndk-build -C <some_project>
NDK_TOOLCHAIN_VERSION=snapdragonclang3.4 NDK_VERSION=r9 ndk-build -C <some_project>
NDK_TOOLCHAIN_VERSION=snapdragonclang3.4 NDK_VERSION=r8e ndk-build -C <some_project>

7. It is assumed that Snapdragon LLVM ARM Compiler 3.4 would be used on 64 bit
systems with Android NDK for 64 bit and on 32 bit systems with Android NDK for
32 bit.

8. A standalone toolchain for the Android NDK environment using the Snapdragon
LLVM ARM compiler can be created using the make-standalone-toolchain.sh Android
NDK utility. For example, to create a standalone toolchain for Linux 64-bit
environment, the following command can be used:

<NDK_ROOT>/build/tools/make-standalone-toolchain.sh --ndk-dir=<NDK_ROOT>
--platform=android-9 --arch=arm --install-dir=<some_dir>
--toolchain=arm-linux-androideabi-4.7
--llvm-version=Snapdragon_LLVM_for_Android_3.4 --system=linux-x86_64

The above command line specifies that android-9 platform directory must be
copied to be used as the standalone sysroot and GCC-4.7 toolchain must be
copied to be used as the linker and for other binutils such as objdump.

NOTE:
The Android NDK r9c has an incompatible definition in its header file math.h
that causes compilation errors with LLVM (both Snapdragon LLVM and llvm.org
LLVM). This issue has been reported to Google and the fix will be available in
the next release of Android NDK. To use r9c, please comment out the following
line in the file <NDK_ROOT>/platforms/android-xx/arch-arm/usr/include/math.h

int     __builtin_isnan(double) __NDK_FPABI_MATH__ __pure2;


Contacts & Bug Reporting
http://developer.qualcomm.com/llvm-forum
