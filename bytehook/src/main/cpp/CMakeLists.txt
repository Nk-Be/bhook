cmake_minimum_required(VERSION 3.22.1)
project(bytehook C)

file(GLOB SRC *.c)

if((${ANDROID_ABI} STREQUAL "arm64-v8a") OR (${ANDROID_ABI} STREQUAL "x86_64"))
    set(ARCH_LINK_FLAGS "-Wl,-z,max-page-size=16384")
else()
    set(ARCH_LINK_FLAGS "")
endif()

# Static library for embedding (NPatch, NyaZygisk, etc.)
add_library(bytehook_static STATIC ${SRC})
target_compile_features(bytehook_static PRIVATE c_std_11)
target_include_directories(bytehook_static PUBLIC include)
target_include_directories(bytehook_static PRIVATE . third_party/bsd third_party/lss)
target_compile_options(bytehook_static PRIVATE -Oz -flto -fvisibility=hidden -ffunction-sections -fdata-sections)
target_link_libraries(bytehook_static PUBLIC log)
set_target_properties(bytehook_static PROPERTIES OUTPUT_NAME "bytehook")

# Shared library for standard Android/Gradle usage
add_library(bytehook SHARED ${SRC})
target_compile_features(bytehook PRIVATE c_std_11)
target_include_directories(bytehook PUBLIC include)
target_include_directories(bytehook PRIVATE . third_party/bsd third_party/lss)
target_compile_options(bytehook PRIVATE -Oz -flto -fvisibility=hidden -ffunction-sections -fdata-sections)
target_link_options(bytehook PRIVATE ${ARCH_LINK_FLAGS} -Wl,--exclude-libs,ALL -Wl,--gc-sections -Wl,--version-script=${CMAKE_CURRENT_SOURCE_DIR}/bytehook.map.txt)
target_link_libraries(bytehook log)
set_target_properties(bytehook PROPERTIES OUTPUT_NAME "bytehook")
