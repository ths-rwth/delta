cmake_minimum_required (VERSION 3.7 FATAL_ERROR)
set(CMAKE_DISABLE_IN_SOURCE_BUILD ON)

project(delta CXX)

if("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")
   message(SEND_ERROR "In-source builds are not allowed.")
endif()

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

set(PROJECT_FULLNAME "delta")
set(PROJECT_DESCRIPTION "Delta debugging tool.")

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR})
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "DEBUG" "RELEASE" "RELWITHDEBINFO")
if (NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE "RELEASE")
endif()

set(DYNAMIC_EXT ".so")
set(STATIC_EXT ".a")

# find Boost
set(BOOST_COMPONENTS "program_options")
set(Boost_USE_DEBUG_RUNTIME "OFF")
find_package(Boost 1.65.1 COMPONENTS ${BOOST_COMPONENTS})

add_subdirectory(src)