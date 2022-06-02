cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

project(test_benchTests LANGUAGES CXX)

# ---- Options ----

option(ENABLE_TEST_COVERAGE "Enable test coverage" OFF)
option(TEST_INSTALLED_VERSION "Test the version found by find_package" OFF)

# --- Import tools ----

include(../cmake/tools.cmake)

# ---- Dependencies ----

include(../cmake/CPM.cmake)

CPMAddPackage("gh:onqtam/doctest#2.4.5")
CPMAddPackage("gh:TheLartians/Format.cmake@1.7.0")

if(TEST_INSTALLED_VERSION)
  find_package(test_bench REQUIRED)
else()
  CPMAddPackage(NAME test_bench SOURCE_DIR ${CMAKE_CURRENT_LIST_DIR}/..)
endif()

# ---- Create binary ----

file(GLOB sources CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/source/*.cpp)
add_executable(test_benchTests ${sources})
target_link_libraries(test_benchTests doctest::doctest test_bench::test_bench)
set_target_properties(test_benchTests PROPERTIES CXX_STANDARD 17)

# enable compiler warnings
if(NOT TEST_INSTALLED_VERSION)
  if(CMAKE_CXX_COMPILER_ID MATCHES "Clang" OR CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    target_compile_options(test_bench PUBLIC -Wall -Wpedantic -Wextra -Werror)
  elseif(MSVC)
    target_compile_options(test_bench PUBLIC /W4 /WX)
    target_compile_definitions(test_benchTests PUBLIC DOCTEST_CONFIG_USE_STD_HEADERS)
  endif()
endif()

# ---- Add test_benchTests ----

enable_testing()

# Note: doctest and similar testing frameworks can automatically configure CMake tests. For other
# testing frameworks add the tests target instead: add_test(NAME test_benchTests COMMAND test_benchTests)

include(${doctest_SOURCE_DIR}/scripts/cmake/doctest.cmake)
doctest_discover_tests(test_benchTests)

# ---- code coverage ----

if(ENABLE_TEST_COVERAGE)
  target_compile_options(test_bench PUBLIC -O0 -g -fprofile-arcs -ftest-coverage)
  target_link_options(test_bench PUBLIC -fprofile-arcs -ftest-coverage)
endif()
