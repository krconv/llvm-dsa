cmake_minimum_required(VERSION 2.8.11)
project(DSA)

find_package (LLVM 3.8.0 REQUIRED CONFIG)

include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

# Add path for custom modules
list (APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

# put static libraries first
set(CMAKE_FIND_LIBRARY_SUFFIXES ".a" ${CMAKE_FIND_LIBRARY_SUFFIXES})

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${LLVM_CXXFLAGS}")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${LLVM_LDFLAGS}")

set(LLVM_REQUIRES_RTTI TRUE)
set(LLVM_REQUIRES_EH TRUE)
link_directories(${LLVM_LIBRARY_DIR})

# We incorporate the CMake features provided by LLVM:
list(APPEND CMAKE_MODULE_PATH "${LLVM_CMAKE_DIR}")
include(AddLLVM)
link_directories(${LLVM_LIBRARY_DIRS})

# check for rt lib. Not needed on OSX.
find_library(RT_LIB NAMES rt)
if (NOT RT_LIB)
  set(RT_LIB "")
endif()
mark_as_advanced(RT_LIB)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

install(DIRECTORY include/
  DESTINATION include
  FILES_MATCHING
  PATTERN "*.h"
  PATTERN ".svn" EXCLUDE)

add_definitions(-std=c++11)
add_definitions(-Wno-redeclared-class-member -Wno-sometimes-uninitialized)
add_definitions(-Wno-covered-switch-default)

if ("${CMAKE_CXX_COMPILER_ID}" MATCHES "GNU")
  add_definitions( -Wno-unused-local-typedefs)
endif ()

add_subdirectory (lib/AssistDS)
add_subdirectory (lib/DSA)
