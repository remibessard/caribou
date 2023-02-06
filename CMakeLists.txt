cmake_minimum_required(VERSION 3.12)
project(Caribou VERSION 21.06.00)

# Policies
cmake_policy(SET CMP0072 NEW)
cmake_policy(SET CMP0074 NEW)
cmake_policy(SET CMP0076 NEW)
cmake_policy(SET CMP0094 NEW)

set (CARIBOU_VERSION ${PROJECT_VERSION})

include(cmake/environment.cmake)


# VTK option
set(CARIBOU_VTK_MODULES CommonCore CommonDataModel IOLegacy)
find_package(VTK QUIET)
if (VTK_FOUND AND VTK_VERSION VERSION_LESS "9.0")
    set(CARIBOU_VTK_MODULES vtkCommonCore vtkCommonDataModel vtkIOLegacy)
endif()
find_package(VTK COMPONENTS ${CARIBOU_VTK_MODULES} QUIET)
CMAKE_DEPENDENT_OPTION(CARIBOU_WITH_VTK "Compile the plugin with VTK support." ON "VTK_FOUND" OFF)
if (CARIBOU_WITH_VTK)
    foreach(VTK_MODULE ${CARIBOU_VTK_MODULES})
        list(APPEND _VTK_INCLUDE_DIRECTORIES "${${VTK_MODULE}_INCLUDE_DIRS}")
        list(APPEND _VTK_LIBRARY_DIRECTORIES "${${VTK_MODULE}_RUNTIME_LIBRARY_DIRS}")
        list(APPEND _VTK_LIBRARIES "${${VTK_MODULE}_LIBRARIES}")
    endforeach()
    list(REMOVE_DUPLICATES _VTK_INCLUDE_DIRECTORIES)
    list(REMOVE_DUPLICATES _VTK_LIBRARY_DIRECTORIES)
    list(REMOVE_DUPLICATES _VTK_LIBRARIES)
    set(VTK_MSG "Caribou with VTK support")
    if (VTK_PREFIX_PATH)
        set(VTK_MSG "${VTK_MSG}\n\tPrefix: ${VTK_PREFIX_PATH}")
    endif()
    if (_VTK_INCLUDE_DIRECTORIES)
        set(VTK_MSG "${VTK_MSG}\n\tInclude directories: ${_VTK_INCLUDE_DIRECTORIES}")
    endif ()
    if (_VTK_LIBRARY_DIRECTORIES)
        set(VTK_MSG "${VTK_MSG}\n\tLibrary directories: ${_VTK_LIBRARY_DIRECTORIES}")
    endif ()
    if (_VTK_LIBRARIES)
        set(VTK_MSG "${VTK_MSG}\n\tLibraries directories: ${_VTK_LIBRARIES}")
    endif ()
    set(VTK_MSG "${VTK_MSG}\n\tVersion: ${VTK_VERSION}")
    message(STATUS "${VTK_MSG}")
endif()

set(CARIBOU_SOURCE_DIR src/Caribou)

# Include CMake specific tools
include(CMakeDependentOption)

include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-march=native" COMPILER_SUPPORTS_MARCH_NATIVE)
CMAKE_DEPENDENT_OPTION(CARIBOU_OPTIMIZE_FOR_NATIVE "Build with -march=native to allow msse support" ON "COMPILER_SUPPORTS_MARCH_NATIVE" OFF)

# OPTIONS
option(CARIBOU_USE_FLOAT "Use float precision for floating point values." OFF)

# SOFA option
find_package(Threads QUIET)
find_package(Sofa.Framework QUIET)

CMAKE_DEPENDENT_OPTION(CARIBOU_WITH_SOFA "Compile the SofaCaribou library" ON "Sofa.Framework_FOUND" OFF)
if (CARIBOU_WITH_SOFA)
    list(APPEND CMAKE_PREFIX_PATH ${SOFA_MODULE_DIR})
    list(REMOVE_DUPLICATES CMAKE_PREFIX_PATH)
    find_package(SofaPython3 QUIET) # We need to do this first to setup the exact python version required
endif()


# Tests
# SOFA is exporting its own gtest, let's try to find it first
find_package(GTest CONFIG QUIET)
if (NOT GTest_FOUND)
    # This find_package needs to be executed at MOST once, else it will fails on the second call
    find_package(GMock QUIET)
endif ()
CMAKE_DEPENDENT_OPTION(CARIBOU_BUILD_TESTS "Compile the automatic tests for Caribou." ON "GTest_FOUND OR GMock_FOUND" OFF)
if (CARIBOU_BUILD_TESTS)
    if (TARGET gtest)
        message(STATUS "Caribou with unittests")
    else()
        add_library(gtest SHARED IMPORTED)
        set_target_properties(gtest PROPERTIES
            INTERFACE_INCLUDE_DIRECTORIES "${GTEST_INCLUDE_DIRS}"
            IMPORTED_LOCATION "${GTEST_BOTH_LIBRARIES}"
        )
        message(STATUS "Caribou with unittests\n\tLibraries: ${GTEST_BOTH_LIBRARIES}\n\tInclude directories: ${GTEST_INCLUDE_DIRS}")
    endif ()
elseif(NOT GTest_FOUND AND NOT GMock_FOUND)
    message(STATUS "Caribou without unittests (missing GTest)")
endif()

# Benchmarks
OPTION(CARIBOU_BUILD_BENCHMARKS "Compile the benchmarks for Caribou." ON)
if (CARIBOU_BUILD_BENCHMARKS)
    message(STATUS "Caribou with benchmarks")
else()
    message(STATUS "Caribou without benchmarks")
endif()

# SOFA Python3
find_file(SofaPython3Tools NAMES "SofaPython3/lib/cmake/SofaPython3/SofaPython3Tools.cmake")
if(SofaPython3Tools)
    message("-- Found SofaPython3Tools. Python3 packages will be installed.")
    include(${SofaPython3Tools})
    set(SP3_PYTHON_PACKAGES_DIRECTORY "python3/site-packages")
else()
    # try again with the find_package mechanism
    find_package(SofaPython3 QUIET)
endif()
CMAKE_DEPENDENT_OPTION(CARIBOU_WITH_PYTHON_3 "Compile the plugin with python 3 support" ON "SofaPython3_FOUND OR SofaPython3Tools" OFF)
if(CARIBOU_WITH_PYTHON_3)
    message(STATUS "Caribou with Python support:
    Version: ${Python_VERSION}
    Executable: ${Python_EXECUTABLE}
    Headers: ${Python_INCLUDE_DIRS}
    Libraries: ${Python_LIBRARIES}
    pybind11 version: ${pybind11_VERSION}
    pybind11 config: ${pybind11_CONFIG}"
    )
    get_filename_component(SP3_ROOT_DIR "${SofaPython3_DIR}/../../.." ABSOLUTE)
    message(STATUS
    "Caribou with SofaPython3 support\n"
    "    SP3 version: ${SofaPython3_VERSION}\n"
    "    SP3 location: ${SP3_ROOT_DIR}"
    )

    if (CARIBOU_WITH_SOFA)
        get_filename_component(SOFA_ROOT_DIR "${SofaFramework_DIR}/../../.." ABSOLUTE)
        message(STATUS
               "Caribou with SOFA support\n"
               "    SOFA version: ${Sofa_VERSION}\n"
        )
        SP3_add_python_package(
            SOURCE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/sofaCaribou
            TARGET_DIRECTORY SofaCaribou
        )
    endif()

endif()

# OpenMP option
set(CMAKE_FIND_LIBRARY_SUFFIXES_BEFORE ${CMAKE_FIND_LIBRARY_SUFFIXES})
SET(CMAKE_FIND_LIBRARY_SUFFIXES .so)
find_package(OpenMP QUIET)
set(CMAKE_FIND_LIBRARY_SUFFIXES ${CMAKE_FIND_LIBRARY_SUFFIXES_BEFORE})
CMAKE_DEPENDENT_OPTION(CARIBOU_WITH_OPENMP "Compile the plugin with OpenMP support" ON "OPENMP_FOUND" OFF)
if (CARIBOU_WITH_OPENMP)
    message(STATUS "Caribou with OpenMP support\n\tLibraries: ${OpenMP_CXX_LIBRARIES}")
endif()

# MKL option
set(MKL_STATIC ON)
if (NOT CARIBOU_WITH_OPENMP)
    set(MKL_THREADING_VENDOR SEQUENTIAL)
endif()
find_package(MKL QUIET)
CMAKE_DEPENDENT_OPTION(CARIBOU_WITH_MKL "Compile the plugin with MKL support." ON "MKL_FOUND" OFF)
if (CARIBOU_WITH_MKL)
    message(STATUS "Caribou with MKL support\n\tInclude directories: ${MKL_INCLUDE_DIRS}\n\tLibraries: ${MKL_LIBRARIES}\n\tFlags: ${MKL_LINKER_FLAGS}")
endif()

# Compiler options
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED 17)
if(NOT WIN32)
    if ("${CMAKE_CXX_COMPILER} ${CMAKE_CXX_COMPILER_ARG1}" MATCHES ".*[cC][lL][aA][nN][gG]")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fcolor-diagnostics")
    else()
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fdiagnostics-color")
    endif()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -pedantic")
endif()

add_subdirectory(src/Caribou)

if (CARIBOU_WITH_SOFA)
    add_subdirectory(src/SofaCaribou)
endif()

if (CARIBOU_BUILD_TESTS)
    add_subdirectory(unittest)
endif()

if (CARIBOU_BUILD_BENCHMARKS)
    add_subdirectory(Benchmark)
endif()

install(
       FILES
        "${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/FindSOFA.cmake"
        "${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/FindMKL.cmake"
        DESTINATION plugins/SofaCaribou/lib/cmake/SofaCaribou
        COMPONENT headers
)
