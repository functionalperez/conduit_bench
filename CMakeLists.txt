cmake_minimum_required(VERSION 3.0.0)
project(conduit_bench VERSION 0.1.0)

add_compile_options("-fPIC" "-std=c++20")

set(BUILD_SHARED_LIBS ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "../bin")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "../lib")

set(POSITION_INDEPENDENT_CODE ON)

add_subdirectory(benchmark)
add_subdirectory(fmt)
add_subdirectory(tuplet)
add_subdirectory(arglet)
include_directories(cppcoro/include)

add_library(bench INTERFACE)
target_include_directories(
    bench
    INTERFACE
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include/>
    $<INSTALL_INTERFACE:include>)

target_link_libraries(bench INTERFACE benchmark dl fmt tuplet)

add_executable(conduit_bench main.cpp)
target_link_libraries(conduit_bench bench arglet)

add_library(baseline SHARED MODULE "src/baseline.cpp")
target_link_libraries(baseline benchmark)

add_library(cppcoro SHARED MODULE "src/cppcoro.cpp")
target_link_libraries(cppcoro benchmark)
target_include_directories(cppcoro PRIVATE "conduit-main/include")

add_library(conduit-main SHARED MODULE "src/conduit.cpp")
target_link_libraries(conduit-main benchmark)
target_include_directories(conduit-main PRIVATE "conduit-main/include")

add_library(conduit-dev SHARED MODULE "src/conduit.cpp")
target_link_libraries(conduit-dev benchmark)
target_include_directories(conduit-dev PRIVATE "conduit-dev/include")
