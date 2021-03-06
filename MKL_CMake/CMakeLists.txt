cmake_minimum_required (VERSION 3.16)
project(MKL_CMAKE VERSION 1.0.0)

# Find the BLAS stuff
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

set(BLA_DEFINITIONS)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -O0 -Wall")
if ("$ENV{MKLROOT}" STREQUAL "")
  message(FATAL_ERROR "MKLROOT is not set. Please source the Intel MKL mklvars.sh file.")
endif()

# user defined options for MKL
option(MKL_USE_parallel "Use MKL parallel" True)
option(MKL_USE_sdl "Single Dynamic Library or static/dynamic" False)
set(MKL_USE_interface "lp64" CACHE STRING "for Intel(R)64 compatible arch: ilp64/lp64 or for ia32 arch: cdecl/stdcall")

if (BLA_VENDOR MATCHES "_seq")
  set(MKL_USE_parallel OFF)
else()
  set(MKL_USE_parallel ON)
endif()

# find_package(GTest REQUIRED) # unit testing purpose
# find_package(Boost REQUIRED) # multi purpose
find_package(MKL REQUIRED)

if (MKL_FOUND)
  set(BLA_INCLUDE_DIR ${MKL_INCLUDE_DIR})
  set(BLAS_LIBRARIES ${MKL_LIBRARIES})
  set(BLA_DEFINITIONS USE_MKL)
endif()

message(STATUS "BLAS Libraries: ${BLAS_LIBRARIES}")

add_executable(main main.cpp)

# Indicate which libraries to include during the link process.
# target_link_libraries(main intel_src::intel_src ${GTEST_LIBRARIES} pthread)
target_link_libraries(main ${MKL_LIBRARIES})
