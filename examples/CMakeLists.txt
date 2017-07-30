################################################################################
# examples/CMakeLists.txt
#
# Part of Project Thrill - http://project-thrill.org
#
# Copyright (C) 2015 Matthias Stumpp <mstumpp@gmail.com>
# Copyright (C) 2016 Timo Bingmann <tb@panthema.net>
#
# All rights reserved. Published under the BSD-2 license in the LICENSE file.
################################################################################

cmake_minimum_required(VERSION 3.0)

include("cmake/Hunter/HunterGate.cmake")
HunterGate(
        URL "https://github.com/ruslo/hunter/archive/v0.16.24.tar.gz"
        SHA1 "00ab627d3d4fe5151e15b6ab2b1b1da17650984a"
)

# custom cmake scripts
#set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/misc/cmake)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
# project: we only use C++
project(thrillexample)

hunter_add_package(ZLIB)
find_package(ZLIB)
hunter_add_package(BZip2)
find_package(BZip2)
hunter_add_package(thrill)
find_package(thrill)
hunter_add_package(tlx)
find_package(tlx)
hunter_add_package(GTest)
find_package(GTest)

include_directories(${CMAKE_SOURCE_DIR}/..)
include_directories(${trhill_dir})

# macro for building main thrill programs with correct libraries
macro(thrill_build TARGETNAME)

  add_executable(${TARGETNAME} ${ARGN})
  target_link_libraries(${TARGETNAME} thrill::thrill)

endmacro(thrill_build)

# macro for building a single-source thrill program
macro(thrill_build_prog PROGNAME)

  string(REPLACE "/" "_" TARGETNAME "${PROGNAME}") # replace slashes

  thrill_build(${TARGETNAME} ${PROGNAME}.cpp ${ARGN})

endmacro(thrill_build_prog)

# macro for registering test programs: maybe prepend valgrind, add environment
# Usage: thrill_test_single(testname "ENV=1;ENV2=1" program args)
macro(thrill_test_single TESTNAME ENVIRON PROG)

    add_test(
      NAME ${TESTNAME}
      COMMAND ${PROG} ${ARGN})

  # environment of test run: set default and let ENVIRON override
  set(TEST_ENVIRON
    "THRILL_NET=mock;THRILL_LOCAL=4;THRILL_WORKERS_PER_HOST=1;"
    "THRILL_LOG=;THRILL_DIE_WITH_PARENT=;THRILL_UNLINK_BINARY=;"
    "${ENVIRON}")

  set_tests_properties(${TESTNAME} PROPERTIES ENVIRONMENT "${TEST_ENVIRON}")

endmacro(thrill_test_single)

# macro for registering test programs: prepends environment variables
# Usage: thrill_test_multiple(testname program args)
macro(thrill_test_multiple TESTNAME)

  thrill_test_single("${TESTNAME}3" "THRILL_LOCAL=3" ${ARGN})
  thrill_test_single("${TESTNAME}4" "THRILL_LOCAL=4" ${ARGN})

endmacro(thrill_test_multiple)

enable_testing()
include(CTest)

add_subdirectory(k-means)
add_subdirectory(logistic_regression)
add_subdirectory(page_rank)
add_subdirectory(percentiles)
add_subdirectory(select)
add_subdirectory(sleep)
add_subdirectory(suffix_sorting)
add_subdirectory(terasort)
add_subdirectory(tpch)
add_subdirectory(triangles)
add_subdirectory(tutorial)
add_subdirectory(vfs_tool)
add_subdirectory(word_count)
add_subdirectory(stochastic_gradient_descent)
add_subdirectory(bfs)

################################################################################
