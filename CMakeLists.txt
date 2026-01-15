cmake_minimum_required(VERSION 3.1...3.14)

project(ccc LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
find_package(Threads)

if(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /std:c++17")
else()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17")
endif(MSVC)

## ICU library path detection (for Boost.Locale/Boost.Regex)
## Try to find icu4c via homebrew opt symlinks (version-independent)
if(EXISTS "/opt/homebrew/opt/icu4c@78/lib")
    SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -L/opt/homebrew/opt/icu4c@78/lib")
elseif(EXISTS "/opt/homebrew/opt/icu4c/lib")
    SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -L/opt/homebrew/opt/icu4c/lib")
elseif(EXISTS "/usr/local/opt/icu4c/lib")
    SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -L/usr/local/opt/icu4c/lib")
endif()

#################$
# Origin Library #
##################

include_directories("third_party/asutton/origin")

#################
# Boost Library #
#################
set(Boost_USE_STATIC_LIBS ON)
set(Boost_USE_MULTITHREADED ON)
add_definitions(-D_WIN32_WINNT=0x0501)
# Use Boost's native CMake config instead of deprecated FindBoost module
cmake_policy(SET CMP0167 NEW)
# Note: 'system' component is header-only since Boost 1.69 and removed in recent versions
find_package(Boost COMPONENTS regex date_time filesystem unit_test_framework program_options REQUIRED)
# Use Boost::headers target to get include directories (modern CMake approach)
# For global include access, we link Boost::headers to all targets via include_directories
get_target_property(BOOST_INCLUDE_DIRS Boost::headers INTERFACE_INCLUDE_DIRECTORIES)
include_directories(${BOOST_INCLUDE_DIRS})

macro(boosttest_add_test TESTNAME)
    add_executable(${TESTNAME} ${ARGN})
    target_link_libraries(${TESTNAME} PRIVATE Boost::filesystem
                                PRIVATE Boost::unit_test_framework)
    add_test(NAME ${TESTNAME} COMMAND ${TESTNAME})
    set_target_properties(${TESTNAME} PROPERTIES FOLDER tests)
endmacro()

#######################
# Google Test Library #
#######################

# Prevent GoogleTest from overriding our compiler/linker options
# when building with Visual Studio
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

add_subdirectory("${PROJECT_SOURCE_DIR}/third_party/google/googletest" "third_party/google/googletest")
mark_as_advanced(
    BUILD_GMOCK BUILD_GTEST BUILD_SHARED_LIBS
    gmock_build_tests gtest_build_samples gtest_build_tests
    gtest_disable_pthreads gtest_force_shared_crt gtest_hide_internal_symbols
)

set_target_properties(gtest PROPERTIES FOLDER third_party)
set_target_properties(gtest_main PROPERTIES FOLDER third_party)
set_target_properties(gmock PROPERTIES FOLDER third_party)
set_target_properties(gmock_main PROPERTIES FOLDER third_party)

macro(googletest_add_test TESTNAME)
    add_executable(${TESTNAME} ${ARGN})
    target_link_libraries(${TESTNAME} PRIVATE gtest PRIVATE gmock PRIVATE gtest_main)
    add_test(NAME ${TESTNAME} COMMAND ${TESTNAME})
    set_target_properties(${TESTNAME} PROPERTIES FOLDER tests)
endmacro()

#################
# Catch Library #
#################

add_library(Catch2 INTERFACE)
target_include_directories(Catch2 INTERFACE ${CMAKE_SOURCE_DIR}/third_party/catchorg/Catch2/single_include)

macro(catch_add_test TESTNAME)
    add_executable(${TESTNAME} ${ARGN})
    target_link_libraries(${TESTNAME} PRIVATE Catch2)
    add_test(NAME ${TESTNAME} COMMAND ${TESTNAME})
    set_target_properties(${TESTNAME} PROPERTIES FOLDER tests)
endmacro()

################
# Enable CTest #
################

enable_testing()

############
# Chapters #
############

add_subdirectory(preface)
add_subdirectory(chapter_1)
add_subdirectory(chapter_2)
add_subdirectory(chapter_3)
add_subdirectory(chapter_4)
add_subdirectory(chapter_5)
add_subdirectory(chapter_6)
add_subdirectory(chapter_7)
add_subdirectory(chapter_8)
add_subdirectory(chapter_9)
add_subdirectory(chapter_10)
add_subdirectory(chapter_11)
add_subdirectory(chapter_12)
add_subdirectory(chapter_13)
add_subdirectory(chapter_14)
add_subdirectory(chapter_15)
add_subdirectory(chapter_16)
add_subdirectory(chapter_17)
add_subdirectory(chapter_18)
add_subdirectory(chapter_19)
add_subdirectory(chapter_20)
add_subdirectory(chapter_21)
