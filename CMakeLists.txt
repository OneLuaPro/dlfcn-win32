cmake_minimum_required(VERSION 3.15)

if(POLICY CMP0091)
  cmake_policy(SET CMP0091 NEW)
endif()

if (NOT DEFINED CMAKE_BUILD_TYPE)
  set (CMAKE_BUILD_TYPE Release CACHE STRING "Build type")
endif ()

project (dlfcn-win32 C)

list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/modules")
include(Macros)

option(BUILD_SHARED_LIBS "shared/static libs" ON) 
option(BUILD_TESTS "tests?" OFF)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

if (MSVC)
    add_compile_options(/W4)
    if(CMAKE_C_COMPILER_ID STREQUAL "IntelLLVM")
        add_link_options("/NODEFAULTLIB:libmmd.lib")
        link_libraries(libmmt.lib libircmt.lib)
    endif()
else()
    add_compile_options(-Wall)
endif()

if(WIN32)
    add_subdirectory(src)
endif()

if (BUILD_TESTS)
    if (CMAKE_CROSSCOMPILING AND NOT DEFINED CMAKE_CROSSCOMPILING_EMULATOR)
        message(WARNING "You might want to set CMAKE_CROSSCOMPILING_EMULATOR to wine path in order to run tests")
    endif ()
    enable_testing()
    add_subdirectory(tests)
endif()
