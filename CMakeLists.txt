# set appropriate cmake version to enable CUDA
cmake_minimum_required(VERSION 3.8 FATAL_ERROR)

# set path to CUDA compiler
set(CMAKE_CUDA_COMPILER "/usr/local/cuda/bin/nvcc")
project(CUDA-CLion-CMake-Linux LANGUAGES CXX CUDA)

# ========================================================================== #
# language standard requirements
# ========================================================================== #

if (NOT DEFINED CMAKE_C_STANDARD)
    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_STANDARD_REQUIRED true)
endif ()

if (NOT DEFINED CMAKE_CUDA_STANDARD)
    set(CMAKE_CUDA_STANDARD 11)
    set(CMAKE_CUDA_STANDARD_REQUIRED true)
endif ()

# ========================================================================== #
# compilation flags
# ========================================================================== #

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

include(CheckCXXCompilerFlag)

check_cxx_compiler_flag("-Wall" COMPILER_CXX_ALL_WARN)
if (COMPILER_CXX_ALL_WARN)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall"
            CACHE STRING "Show all warning messages" FORCE
            )
endif ()

check_cxx_compiler_flag("-Wextra" COMPILER_CXX_EXTRA_WARN)
if (COMPILER_CXX_EXTRA_WARN)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wextra"
            CACHE STRING "Show extra warning messages" FORCE
            )
endif ()

check_cxx_compiler_flag("-pipe" COMPILER_CXX_PIPE)
if (COMPILER_CXX_PIPE)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pipe"
            CACHE STRING "Speed up compilation by piping" FORCE
            )
endif ()

set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -Xcompiler='${CMAKE_C_FLAGS}'"
        CACHE STRING "Pass the same C flags to back-end compiler of nvcc" FORCE
        )

check_cxx_compiler_flag("-mavx" COMPILER_CXX_AVX)
if (COMPILER_CXX_AVX)
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -mavx"
            CACHE STRING "Optimize with AVX instructions" FORCE
            )
endif ()

check_cxx_compiler_flag("-mavx2" COMPILER_CXX_AVX2)
if (COMPILER_CXX_AVX2)
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -mavx2"
            CACHE STRING "Optimize with AVX2 instructions" FORCE
            )
endif ()

check_cxx_compiler_flag("-march=native" COMPILER_CXX_ARCH)
if (COMPILER_CXX_ARCH)
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -march=native"
            CACHE STRING "Optimize based on the architecture" FORCE
            )
endif ()

check_cxx_compiler_flag("-mtune=native" COMPILER_CXX_TUNE)
if (COMPILER_CXX_TUNE)
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -mtune=native"
            CACHE STRING "Fine-tune the program based on the CPU" FORCE
            )
endif ()

check_cxx_compiler_flag("-fomit-frame-pointer" COMPILER_CXX_NO_FP)
if (COMPILER_CXX_NO_FP)
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -fomit-frame-pointer"
            CACHE STRING "Omit frame pointers" FORCE
            )
endif ()

check_cxx_compiler_flag("-ffast-math" COMPILER_CXX_FAST_MATH)
if (COMPILER_CXX_FAST_MATH)
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -ffast-math"
            CACHE STRING "Trade floating point precision for speed" FORCE
            )
endif ()

set(CMAKE_CUDA_FLAGS_RELEASE
        "${CMAKE_CUDA_FLAGS_RELEASE} -Xcompiler '${CMAKE_CXX_FLAGS_RELEASE}'"
        CACHE STRING "Pass the same CXX release flags to back-end compiler of nvcc"
        FORCE
        )


# ========================================================================== #
# source code
# ========================================================================== #

set(CXX_SRCS dummy.cpp)

set(CUDA_SRCS cudaMain.cu vecAdd.cu)

set(CXX_CUDA_HDR cudaHeaders.h cudaMain.h vecAdd.h)

# ========================================================================== #
# main program
# ========================================================================== #

add_executable(${PROJECT_NAME} ${CXX_SRCS} ${CXX_CUDA_HDR} ${CUDA_SRCS} main.cpp)

target_link_libraries(${PROJECT_NAME}
        PRIVATE m
        )

set_target_properties(${PROJECT_NAME} PROPERTIES
        LINKER_LANGUAGE CXX
        CUDA_SEPARABLE_COMPILATION ON
        )
