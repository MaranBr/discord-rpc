cmake_minimum_required (VERSION 3.5)
project (DiscordRPC)

include(GNUInstallDirs)

option(BUILD_EXAMPLES "Build example apps" ON)

# format
file(GLOB_RECURSE ALL_SOURCE_FILES
    examples/*.cpp examples/*.h examples/*.c
    include/*.h
    src/*.cpp src/*.h src/*.c
)

# Set CLANG_FORMAT_SUFFIX if you are using custom clang-format, e.g. clang-format-5.0
find_program(CLANG_FORMAT_CMD clang-format${CLANG_FORMAT_SUFFIX})

if (CLANG_FORMAT_CMD)
    add_custom_target(
        clangformat
        COMMAND ${CLANG_FORMAT_CMD}
        -i -style=file -fallback-style=none
        ${ALL_SOURCE_FILES}
        DEPENDS
        ${ALL_SOURCE_FILES}
    )
endif(CLANG_FORMAT_CMD)

# thirdparty stuff
set(RAPIDJSON ${CMAKE_CURRENT_SOURCE_DIR}/thirdparty/rapidjson-1.1.0)

if (NOT EXISTS "${RAPIDJSON}")
    message(FATAL_ERROR "RapidJSON 1.1.0 not found in ${RAPIDJSON}")
endif()

add_library(rapidjson INTERFACE IMPORTED)
set_target_properties(rapidjson PROPERTIES INTERFACE_INCLUDE_DIRECTORIES "${RAPIDJSON}/include")

# add subdirs

add_subdirectory(src)
if (BUILD_EXAMPLES)
    add_subdirectory(examples/send-presence)
endif(BUILD_EXAMPLES)
