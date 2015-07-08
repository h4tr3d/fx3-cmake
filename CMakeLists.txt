#
# This is sample CMakeLists.txt uses fx3.cmake
#

cmake_minimum_required(VERSION 2.8.8)
include(${CMAKE_SOURCE_DIR}/cmake/fx3.cmake)
project(Fx3CmakeSample)

#
# Options
# =======
# Code validation and CI server friendly
set(WPEDANTIC OFF                              CACHE BOOL    "turn on/off -Wpedantic option for compiller (default: off)")
set(WERROR    OFF                              CACHE BOOL    "turn on/off -Werror option for compiller (default: off)")

# Project settings
fx3_enable_cxx()
#fx3_disable_stdc_libs()
fx3_enable_stdcxx_libs()

# Additional C++ flags
set(COMMON_FLAGS "-fmessage-length=0 -ffunction-sections -fdata-sections -fshort-wchar -Wall -Wextra")
if (WPEDANTIC)
  set(COMMON_FLAGS "${COMMON_FLAGS} -Wpedantic")
endif()
if (WERROR)
  set(COMMON_FLAGS "${COMMON_FLAGS} -Werror")
endif()
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-exceptions -fno-rtti -fno-unwind-tables -std=c++11 ${COMMON_FLAGS}")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${COMMON_FLAGS} -Wwrite-strings -std=c99")

# Some "library"
file(GLOB_RECURSE LIB_SRC "lib/*.cpp" "lib/*.c")
file(GLOB_RECURSE LIB_HDR "lib/*.hpp" "lib/*.h")
include_directories(lib)

# Src
file(GLOB_RECURSE EXE_SRC "src/*.cpp" "src/*.c")
file(GLOB_RECURSE EXE_HDR "src/*.hpp" "src/*.h")

# Form object libraries to speed up building multiple targets
add_library(somelib OBJECT ${LIB_SRC})

fx3_add_target(Fx3CmakeSample
    ${EXE_SRC}
    $<TARGET_OBJECTS:somelib>
)
