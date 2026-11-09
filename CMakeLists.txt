cmake_minimum_required(VERSION 3.16)
project(jsc LANGUAGES CXX)

# Standalone JS8Call dictionary compression and varicode utilities.
# Requires a C++11 compiler and Qt 5 Core development files (no GUI or Fortran).
# Build: cmake -S . -B build && cmake --build build
# Consumers: add_subdirectory(jsc), then link to jsc::jsc and include jsc.h
# or varicode.h. The dictionary tables are compiled into the library.
find_package(Qt5 5.8 REQUIRED COMPONENTS Core)

add_library(jsc STATIC
    jsc.cpp
    jsc.h
    jsc_list.cpp
    jsc_map.cpp
    varicode.cpp
    varicode.h
    crc.h
)
add_library(jsc::jsc ALIAS jsc)

target_compile_features(jsc PUBLIC cxx_std_11)
set_target_properties(jsc PROPERTIES CXX_EXTENSIONS OFF)
target_include_directories(jsc PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")
target_link_libraries(jsc PUBLIC Qt5::Core)
