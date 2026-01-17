# ADD BEGIN
cmake_minimum_required(VERSION 3.12...3.28)

# Set policies to handle deprecated CMake features used by Hunter
if(POLICY CMP0153)
  cmake_policy(SET CMP0153 OLD)  # Allow exec_program in Hunter's FindBoost.cmake
endif()

# ADD END

# Use hunter to download and build some third party dependencies
include("cmake/HunterGate.cmake")

huntergate(URL "https://github.com/cpp-pm/hunter/archive/v0.23.279.tar.gz" SHA1 "0a8ede485c3e9c1ceed8ccb989ab6c0aba1f4db7")

project(CppHighPerformanceCodeExamples)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# ADD BEGIN
# Export compile commands for clangd
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
# ADD END

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED YES)
set(CMAKE_CXX_EXTENSIONS NO)

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4 /await")
endif()

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wno-unused-variable -Wno-unused-but-set-variable")
  if("${CMAKE_CXX_COMPILER_VERSION}" VERSION_GREATER_EQUAL 10.2)
    # Don't bother adding the coroutine flag when using GCC 10.0 or 10.1
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fcoroutines")
  endif()
endif()

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wno-unused-parameter -Wno-unused-variable -Wno-unused-private-field")
  if(${CMAKE_CXX_COMPILER_VERSION} VERSION_GREATER_EQUAL 10)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fcoroutines-ts")
  endif()
endif()

hunter_add_package(Boost)
hunter_add_package(GTest)
hunter_add_package(benchmark)

find_package(Boost CONFIG REQUIRED)
find_package(GTest CONFIG REQUIRED)
find_package(benchmark CONFIG REQUIRED)

add_subdirectory("Chapter01")
add_subdirectory("Chapter02")
add_subdirectory("Chapter02/benchmarks")
add_subdirectory("Chapter03")
add_subdirectory("Chapter03/benchmarks")
add_subdirectory("Chapter04")
add_subdirectory("Chapter05")
add_subdirectory("Chapter05/benchmarks")
add_subdirectory("Chapter06")
add_subdirectory("Chapter07")
add_subdirectory("Chapter08")
add_subdirectory("Chapter09")
add_subdirectory("Chapter10")
add_subdirectory("Chapter10/benchmarks")
add_subdirectory("Chapter11")
add_subdirectory("Chapter12")
add_subdirectory("Chapter13")
add_subdirectory("Chapter14")
add_subdirectory("Chapter14/benchmarks")

enable_testing()
# ADD BEGIN
add_test(NAME chapter01 COMMAND chapter01)
add_test(NAME chapter02 COMMAND chapter02)
add_test(NAME chapter03 COMMAND chapter03)
add_test(NAME chapter04 COMMAND chapter04)
add_test(NAME chapter05 COMMAND chapter05)
add_test(NAME chapter06 COMMAND chapter06)
add_test(NAME chapter07 COMMAND chapter07)
add_test(NAME chapter08 COMMAND chapter08)
add_test(NAME chapter09 COMMAND chapter09)
add_test(NAME chapter10 COMMAND chapter10)
add_test(NAME chapter11 COMMAND chapter11)
add_test(NAME chapter12 COMMAND chapter12)
add_test(NAME chapter13 COMMAND chapter13)
add_test(NAME chapter14 COMMAND chapter14)
# ADD END