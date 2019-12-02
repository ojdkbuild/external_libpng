# Copyright 2016, alex at staticlibs.net
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required ( VERSION 2.8.12 )

# project
project ( external_libpng CXX C )
set ( ${PROJECT_NAME}_STATICLIB_VERSION 1.5.13-4 )
set ( ${PROJECT_NAME}_DESCRIPTION "libpng library build for Staticlibs" )
set ( ${PROJECT_NAME}_URL https://github.com/staticlibs/external_libpng )
include ( ${CMAKE_CURRENT_LIST_DIR}/resources/macros.cmake )

# standalone build
if ( NOT DEFINED CMAKE_LIBRARY_OUTPUT_DIRECTORY )
    set ( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR} )
    set ( ZLIB_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR} )
endif ( )
if ( NOT DEFINED STATICLIB_TOOLCHAIN )
    if ( NOT ${CMAKE_SYSTEM_NAME} STREQUAL "Linux" )
        external_libpng_add_subdirectory ( ${CMAKE_CURRENT_LIST_DIR}/../external_zlib )
    endif ( )
endif ( )

# library
add_subdirectory ( ${CMAKE_CURRENT_LIST_DIR}/resources/libpng_cmake ${CMAKE_CURRENT_BINARY_DIR}/libpng )
add_custom_target ( ${PROJECT_NAME} DEPENDS png )

# pkg-config
set ( ${PROJECT_NAME}_MODULE libpng )
set ( ${PROJECT_NAME}_PC_REQUIRES "zlib" )
set ( ${PROJECT_NAME}_PC_CFLAGS                              "-I${png_LOOKASIDE_DIR}" )
set ( ${PROJECT_NAME}_PC_CFLAGS "${${PROJECT_NAME}_PC_CFLAGS} -I${CMAKE_CURRENT_LIST_DIR}/resources/libpng_cmake/include" )
set ( ${PROJECT_NAME}_PC_LIBS "-L${CMAKE_LIBRARY_OUTPUT_DIRECTORY} -lpng" )
if ( UNIX AND ${CMAKE_CXX_COMPILER_ID} MATCHES "(GNU|Clang)" )
    set ( ${PROJECT_NAME}_PC_LIBS "${${PROJECT_NAME}_PC_LIBS} -lm" )
endif ( )
configure_file ( ${CMAKE_CURRENT_LIST_DIR}/resources/pkg-config.in 
        ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/pkgconfig/libpng.pc )
