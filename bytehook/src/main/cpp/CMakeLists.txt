cmake_minimum_required(VERSION 3.10)
project(bytehook C)

file(GLOB SRC *.c)

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
target_link_libraries(bytehook log)
set_target_properties(bytehook PROPERTIES OUTPUT_NAME "bytehook")
