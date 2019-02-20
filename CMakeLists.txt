#########################################################
#                 Superbuild for RPClab                 #
#               Author : LAGARDE Francois               #
#########################################################

cmake_minimum_required(VERSION 3.13 FATAL_ERROR)

project(RPClab VERSION 0.1 DESCRIPTION "Superbuild for RPClab" HOMEPAGE_URL "https://rpclab.github.io/" LANGUAGES NONE)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 17 CACHE STRING "C++ standard used for compiling" FORCE)
set(CMAKE_CXX_STANDARD_REQUIRED ON CACHE BOOL "C++ standard required" FORCE)
set(CMAKE_CXX_EXTENSIONS OFF CACHE BOOL "Allow C++ extensions" FORCE)

# Set the C standard
set(CMAKE_C_STANDARD 11 CACHE STRING "C standard used for compiling" FORCE)
set(CMAKE_C_STANDARD_REQUIRED ON CACHE BOOL "C standard required" FORCE)
set(CMAKE_C_EXTENSIONS OFF CACHE BOOL "Allow C extensions" FORCE)

option( BUILD_CLHEP "Set to OFF to disable CLHEP build" ON )
set(CLHEP_version    "CLHEP_2_4_1_0")
set(CLHEP_repository      "https://gitlab.cern.ch/CLHEP/CLHEP.git")

# Set default install prefix to project root directory if is not changed by the user
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX "${CMAKE_SOURCE_DIR}/bin" CACHE PATH "Default install path for ${PROJECT_NAME}" FORCE)
endif()

# Set default cmake build type to RelWithDebInfo
# Possible options are: None Debug Release RelWithDebInfo MinSizeRel
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "RelWithDebInfo" CACHE STRING "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel." FORCE )
endif()

set(EXTERNAL_PROJECT_DIR ${CMAKE_SOURCE_DIR}/extern CACHE STRING "" FORCE)

# Default path for cmake modules
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake" "${CMAKE_SOURCE_DIR}/cmake/macros" "${CMAKE_SOURCE_DIR}/cmake/modules" CACHE INTERNAL "Default path for cmake modules" FORCE)

# Append link pathes to rpath list
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE CACHE INTERNAL "Append directories in the linker search path and outside the project to the INSTALL_RPATH" FORCE)
# Add library install path to the rpath list
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib" CACHE INTERNAL "Add library install path to the rpath list" FORCE)

# Enable CTest
include(CTest)
enable_testing()
MARK_AS_ADVANCED( DART_TESTING_TIMEOUT )

# Append current bin install path
list(APPEND CMAKE_PREFIX_PATH ${CMAKE_INSTALL_PREFIX})

include(PackageAdd)

if(BUILD_CLHEP)
Package_Add(
            CLHEP
            GIT_REPOSITORY ${CLHEP_repository}
            GIT_TAG ${CLHEP_version}
            GIT_PROGRESS TRUE
            SOURCE_DIR ${EXTERNAL_PROJECT_DIR}/CLHEP/${CLHEP_version}
            INSTALL_DIR ${CMAKE_INSTALL_PREFIX}/CLHEP/${CLHEP_version}
            UPDATE_COMMAND ""
            CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}/CLHEP/${CLHEP_version}
            BUILD_COMMAND make -j4
            LIST_SEPARATOR %
           )
endif()

