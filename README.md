# fx3-cmake
Use CMake build system for Cypress FX3

To use this toolchain to develop for Cypress FX3 simple include it to *CMakeLists.txt* like
```cmake
include(${CMAKE_SOURCE_DIR}/cmake/fx3.cmake)
```
You *must* do it *BEFORE* project declaration (`project()`)

Complete sample for including:
```cmake
cmake_minimum_required(VERSION 2.8.8)
set(FX3_SDK_DIR ${CMAKE_SOURCE_DIR}/sdk/1.3.3)
include(${CMAKE_SOURCE_DIR}/cmake/fx3.cmake)
project(Fx3Project)
...
```

After that you can use next new commands:
- `fx3_enable_cxx()`<br/>
    enable C++ support for project. Disabled by default.
    You can check it via `FX3_CXX` variable.
- `fx3_disable_stdc_libs()`<br/>
    disable linking with standard C and gcc libs. Enabled by default.
    Note, that you must provide some function implementations for correct
    linking.
    You can check it via `FX3_USE_STDC_LIB` variable.
- `fx3_enable_stdcxx_libs()`<br/>
    enable linking with standard C++ library. Disabled by default (increase
    target size). Note, you must provide some implementation for needed
    function with disabled std c++ libs and enabled C++ support
    You can check it via `FX3_USE_STDCXX_LIB` variable.
- `fx3_add_target(target ARGS)`<br/>
    add new target. Use it in same way to `add_executable()`. No known
    restrictions for me. This command configure new target to use SDK libs.
    Also, generator for img file is configured.

Next files expected to be present in `CMAKE_SOURCE_DIRECTORY`:
- In all cases:
  - *cyfx_gcc_startup.S*    - startup assembler, can be overriden via `FX3_CORE_ASM_SRC` varible before
                            *fx3.cmake* including
  - *make/fx3cpp.ld*        - linker script
- Project with disabled C++:
  - *cyfxtx.c*              - can be overriden via `FX3_CORE_C_SRC` variable
- Project with enabled C++:
  - *cyfxtx.cpp*            - can be overriden via `FX3_CORE_CXX_SRC` variable
  - *cyfxcppsyscall.cpp*    - can be overriden via `FX3_CORE_CXX_SRC` variable

All files above can be found at the SDK installation directory: *`FX3_INSTALL_PATH`/firmware/common* and
should be distributed with your project.

This script ask some enveropment variables to locate binaries. All of them must be configured during
normal Cypress SDK installation:
- `ARMGCC_INSTALL_PATH` - to locate compilator installation
- `ARMGCC_VERSION`      - to set conrete compilator version
- `FX3_INSTALL_PATH`    - to locate Cypress SDK path

SDK includes and libraries can be overriden by next variable, declared before *fx3.cmake* include:
```cmake
FX3_SDK_DIR
```
for example:
```cmake
  set(FX3_SDK_DIR ${CMAKE_SOURCE_DIR}/sdk/1.3.3)
  include(${CMAKE_SOURCE_DIR}/fx3.cmake)
```

Note, that `elf2img` tool searches at the `PATH` location only.

You can use you own `make` command on Windows. In this case you can pass option `-DUSE_SDK_MAKE=Off`
to `cmake` and make command must be present in `PATH` or pointed via `-DCMAKE_MAKE_PROGRAM=...`

On Windows you must point generator **"MinGW Makefiles"** if SDK `cs-make` is used. Or other valid
generator if your own make is used.

