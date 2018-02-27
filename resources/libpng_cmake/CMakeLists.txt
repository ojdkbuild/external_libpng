# Copyright 2016, akashche at redhat.com
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

# this file is based on https://github.com/glennrp/libpng/blob/v1.6.21/CMakeLists.txt
cmake_minimum_required ( VERSION 2.8.12 )
project ( png C )
include ( ${CMAKE_CURRENT_LIST_DIR}/../macros.cmake )

if ( NOT DEFINED ${PROJECT_NAME}_LOOKASIDE_DIR )
    set ( ${PROJECT_NAME}_LOOKASIDE_DIR ${CMAKE_CURRENT_LIST_DIR}/../../../lookaside_libpng  CACHE INTERNAL "" )
endif ( )

# dependencies
set ( ${PROJECT_NAME}_DEPS zlib )
png_pkg_check_modules ( ${PROJECT_NAME}_DEPS_PC REQUIRED ${PROJECT_NAME}_DEPS )

# check deplibs cache
if ( STATICLIB_USE_DEPLIBS_CACHE )
    set ( ${PROJECT_NAME}_CACHED_LIB_PATH ${STATICLIB_DEPLIBS_CACHE_DIR}/${CMAKE_STATIC_LIBRARY_PREFIX}${PROJECT_NAME}${CMAKE_STATIC_LIBRARY_SUFFIX} )
    if ( EXISTS ${${PROJECT_NAME}_CACHED_LIB_PATH} )
        add_library( ${PROJECT_NAME} STATIC IMPORTED GLOBAL )
        set_target_properties( ${PROJECT_NAME} PROPERTIES IMPORTED_LOCATION ${${PROJECT_NAME}_CACHED_LIB_PATH} )
        message ( STATUS "Using cached library: [${${PROJECT_NAME}_CACHED_LIB_PATH}]" )
        return ( )
    endif ( )
endif ( )

# library
set ( ${PROJECT_NAME}_HEADERS
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/png.h
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngconf.h
        ${CMAKE_CURRENT_LIST_DIR}/include/pnglibconf.h )
set ( ${PROJECT_NAME}_SRC
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngdebug.h
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pnginfo.h
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngpriv.h
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngstruct.h
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/png.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngerror.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngget.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngmem.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngpread.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngread.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngrio.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngrtran.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngrutil.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngset.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngtrans.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngwio.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngwrite.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngwtran.c
        ${${PROJECT_NAME}_LOOKASIDE_DIR}/pngwutil.c )

add_library ( ${PROJECT_NAME} STATIC ${${PROJECT_NAME}_SRC} ${${PROJECT_NAME}_HEADERS} )
target_include_directories ( ${PROJECT_NAME} BEFORE PRIVATE 
        ${${PROJECT_NAME}_LOOKASIDE_DIR}
        ${CMAKE_CURRENT_LIST_DIR}/include
        ${${PROJECT_NAME}_DEPS_PC_INCLUDE_DIRS} )

if ( ${CMAKE_C_COMPILER_ID}x MATCHES "MSVCx" )
    target_compile_definitions ( ${PROJECT_NAME} PRIVATE -D_CRT_SECURE_NO_DEPRECATE )
endif ( )
