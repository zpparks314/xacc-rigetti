cmake_minimum_required(VERSION 3.2 FATAL_ERROR)
project(xacc-rigetti LANGUAGES CXX)

option(RIGETTI_BUILD_TESTS "Build test programs" OFF)
if (FROM_SETUP_PY AND NOT APPLE)
   message(STATUS "Running build from setup.py, linking to static libstdc++")
   set (CMAKE_SHARED_LINKER_FLAGS "-static-libstdc++" CACHE INTERNAL "" FORCE)
endif()

set(CMAKE_STANDARD_REQUIRED ON)
set(CMAKE_CXX_STANDARD 11)
message(STATUS "C++ version ${CXX_STANDARD} configured.")

find_package(XACC REQUIRED)

if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set (CMAKE_INSTALL_PREFIX "${XACC_ROOT}" CACHE PATH "default install path" FORCE )
endif()

set(CppMicroServicesConfig_DIR "${XACC_ROOT}/share/cppmicroservices4/cmake")
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${XACC_ROOT}/share/xacc")
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CppMicroServicesConfig_DIR}")
include(tests)
include(CppMicroServicesConfig)

include_directories(${XACC_INCLUDE_DIRS})
include_directories(${CMAKE_SOURCE_DIR}/utils)

link_directories(${XACC_LIBRARY_DIR})

if(${XACC_HAS_ANTLR})
   add_subdirectory(compiler)
endif()
add_subdirectory(accelerator)
if(PYTHON_INCLUDE_DIR)
   add_subdirectory(python)
endif()

if(RIGETTI_BUILD_TESTS)
   add_subdirectory(examples)
   include_directories(${CMAKE_SOURCE_DIR}/accelerator)
   add_subdirectory(tests)
endif()
   
