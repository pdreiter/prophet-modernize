# prophet-modernize
a repo that helps to update the Prophet APR tool to more modern GNU tools

## Pre-requisites
Prophet uses an older toolchain, please ensure that the following packages are installed:
1. GCC multilib <= 5.0

## Build Process
The build process for Prophet has been consolidated into `build_prophet.bash`, which performs the following:
1. Installs and builds prerequisites (LLVM+CLANG==3.6.2)
   - Applies patches to these to modernize LLVM images for GNU Binutils version is >=2.25, assuming GCC 4.8 install path `/usr/lib/gcc/x86_64-linux-gnu/4.8/`
   - Any change to GCC installation or paths will need to amend prophet/llvm/patches.tgz content
2. Downloads latest Prophet tarball 
3. Builds and test Prophet (you can use Prophet-provided examples or CodeFlaws <=recommend)

## Testing 
To test Prophet, you can use Prophet-provided examples or CodeFlaws <=recommend

## CAVEATS
- Prophet requires LLVM version 3.6.2 which does not play well with modern GLIBC headers. 
- Prophet does not allow for parallelism - temporary directories and files are used and deleted indiscriminantly
- Please note that Prophet manipulates the PATH env variable prepending its tools directory
If you have full paths in your Makefiles/build tool invocations, this will be an issue. I worked around this by generating a custom Makefile (i.e. Makefile.prophet) that basically does what Prophet's pclang.py does, bypassing Prophet's tools

## Full-source code Prophet APR CAVEATS
The following only pertains to full-source Prophet APR tool with 32b binaries
### 32b and 64b support
Prophet build doesn't naturally generate both 32b and 64b libraries. This build environment does, though.
If you don't use CMake to build your test binaries, then this isn't a problem -- you can use the 32b compiled prophet to evaluation 32b binaries, and 64b prophet for 64b binaries. 
However, cmake compiles a 64b test executable to make sure the compiler works and then the resulting binaries are 32b.
Because the build process was not changed, w/a was implemented that dynamically changes path between 32b Prophet and 64b Prophet libraries.
These libpaths need to be managed in the compile scripts - example scripts expect this path:
64b Prophet @ prophet-gpl/  
32b Prophet @ prophet-gpl/32/  


