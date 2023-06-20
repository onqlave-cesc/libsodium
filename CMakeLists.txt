cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

project(libsodium
        VERSION 1.0.18
        LANGUAGES C
        )

# ---- Add sources ----

file(GLOB_RECURSE LIBSODIUM_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/src/*.c")

# ---- Create library ----

add_library(libsodium ${LIBSODIUM_SOURCES})
target_include_directories(libsodium PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/include)

# ---- Set compiler flags ----

if (MSVC)
    target_compile_options(libsodium PRIVATE /W4 /WX)
else()
    target_compile_options(libsodium PRIVATE -Wall -Wextra -pedantic -Werror)
endif()

# ---- Install ----

install(TARGETS libsodium
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
        )
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/include/
        DESTINATION include/libsodium
        FILES_MATCHING PATTERN "*.h"
        )

# ---- CPM.cmake configuration ----

include(modules/CPM.cmake)

CPMAddPackage(
        NAME PackageProject.cmake
        GITHUB_REPOSITORY TheLartians/PackageProject.cmake
        VERSION 1.0
)

packageProject(
        NAME libsodium
        VERSION ${PROJECT_VERSION}
        BINARY_DIR ${PROJECT_BINARY_DIR}
        INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src/libsodium/include
        INCLUDE_DESTINATION include/libsodium
)
