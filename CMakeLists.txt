cmake_minimum_required(VERSION 3.5.0)
project(libsodium VERSION 1.0.32 LANGUAGES C CXX ASM)

INCLUDE(CheckCCompilerFlag)

#----------------------------------------------------------
######################### OPTIONS #########################
#----------------------------------------------------------

OPTION(LIBSODIUM_BUILD_SHARED_LIBRARIES "Build the library dynamically (ON/TRUE) or statically (OFF/FALSE).")
OPTION(ENABLE_BLOCKING_RANDOM           "Enable this switch only if /dev/urandom is totally broken on the target platform.")
OPTION(ENABLE_MINIMAL_BUILD             "Only compile the minimum set of functions required for the high-level API.")
OPTION(ENABLE_TESTS                     "Enable testing.")
OPTION(DISABLE_ASM                      "Disable AMD64 assembly implementations.")
OPTION(DISABLE_PIE                      "Do not produce position independent executables.")
OPTION(DISABLE_SSP                      "Do not compile with -fstack-protector.")

#----------------------------------------------------------
######################### VERSION #########################
#----------------------------------------------------------

SET(VERSION 1.0.18)
SET(SODIUM_LIBRARY_VERSION_MAJOR 1)
SET(SODIUM_LIBRARY_VERSION_MINOR 0)
SET(DLL_VERSION 6)

SET(SODIUM_LIBRARY_VERSION "1:0:18")
#                            | | |
#                     +------+ | +---+
#                     |        |     |
#                  current:revision:age
#                     |        |     |
#                     |        |     +- increment if interfaces have been added
#                     |        |        set to zero if interfaces have been removed
#                     |        |        or changed
#                     |        +- increment if source code has changed
#                     |           set to zero if current is incremented
#                     +- increment if interfaces have been added, removed or changed

CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/include/sodium/version.h.in ${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/include/sodium/version.h)
CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/include/sodium/version.h.in ${CMAKE_CURRENT_BINARY_DIR}/src/libsodium/include/sodium/version.h)


#----------------------------------------------------------
##################### SOURCE / HEADERS ####################
#----------------------------------------------------------

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/src)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/include)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/include/sodium)
#For version.h.in to version.h
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR}/src/libsodium/include/sodium)

file(GLOB_RECURSE libsodium_HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/*.h)

file(GLOB_RECURSE libsodium_SOURCE ${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/*.c)

#----------------------------------------------------------
######################### LIBRARY #########################
#----------------------------------------------------------

#set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/build")


IF(ENABLE_BLOCKING_RANDOM)
    ADD_DEFINITIONS("-DUSE_BLOCKING_RANDOM")
ENDIF(ENABLE_BLOCKING_RANDOM)

IF(LIBSODIUM_BUILD_SHARED_LIBRARIES)
    ADD_LIBRARY(libsodium SHARED ${libsodium_HEADERS} ${libsodium_SOURCE})
ELSE(LIBSODIUM_BUILD_SHARED_LIBRARIES)
    ADD_DEFINITIONS("-DSODIUM_STATIC")
    ADD_LIBRARY(libsodium STATIC ${libsodium_HEADERS} ${libsodium_SOURCE})
ENDIF(LIBSODIUM_BUILD_SHARED_LIBRARIES)

SET_TARGET_PROPERTIES(libsodium PROPERTIES
        LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/lib
        ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/lib
        PROJECT_LABEL "libsodium"
        )