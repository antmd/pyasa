cmake_minimum_required( VERSION 2.8 )

set( CMAKE_INSTALL_PREFIX /usr/local )

project(asa LANGUAGES C)
include(CheckCXXCompilerFlag)

check_cxx_compiler_flag("-std=c++11" SUPPORTS_CXX11)
if( SUPPORTS_CXX11 )
    set( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
else()
    check_cxx_compiler_flag("-std=c++0x" SUPPORTS_CXX0X)
    if( SUPPORTS_CXX0X )
        set( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
    else()
        message("C++11 is not supported")
    endif()
endif()


set(  asa_BASE_DIR ${CMAKE_CURRENT_SOURCE_DIR} )

set( CMAKE_INCLUDE_CURRENT_DIR ON )

# Globbing is not recommended -- CMake cannot tell when new files are added
# But for a generic skeleton...
file( GLOB_RECURSE 
    asa_INCLUDE_FILES 
    ${asa_BASE_DIR}/*.h
    )

set (ASA_USER_SOURCE_FILES
    asa_usr.c
    asa_usr_cst.c
    )

set (ASA_SOURCE_FILES
    asa.c
    )


set( asa_SOURCE_FILES
    ${ASA_USER_SOURCE_FILES}
    ${ASA_SOURCE_FILES}
    )

file(
    COPY ${asa_INCLUDE_FILES} 
    DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/include/asa
    )

include_directories( ${Boost_INCLUDE_DIR} )

set( ALL_SOURCES ${asa_INCLUDE_FILES} ${asa_SOURCE_FILES} )

add_library( asa SHARED ${ALL_SOURCES})
add_library( asa_static STATIC ${ALL_SOURCES})
set_target_properties( asa_static PROPERTIES OUTPUT_NAME asa )

# INSTALL
# -------

install (
    TARGETS asa asa_static
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    )

