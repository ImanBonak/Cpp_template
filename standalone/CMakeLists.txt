cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

project(test_benchStandalone LANGUAGES CXX)

# --- Import tools ----

include(../cmake/tools.cmake)

# ---- Dependencies ----

include(../cmake/CPM.cmake)

CPMAddPackage(
  GITHUB_REPOSITORY jarro2783/cxxopts
  VERSION 2.2.1
  OPTIONS "CXXOPTS_BUILD_EXAMPLES NO" "CXXOPTS_BUILD_TESTS NO" "CXXOPTS_ENABLE_INSTALL YES"
)

CPMAddPackage(NAME test_bench SOURCE_DIR ${CMAKE_CURRENT_LIST_DIR}/..)

# ---- Create standalone executable ----

file(GLOB sources CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/source/*.cpp)

add_executable(test_benchStandalone ${sources})

set_target_properties(test_benchStandalone PROPERTIES CXX_STANDARD 17 OUTPUT_NAME "test_bench_example")

target_link_libraries(test_benchStandalone test_bench::test_bench cxxopts)
