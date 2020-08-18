cmake_minimum_required(VERSION 3.5)
cmake_policy(VERSION 3.5)
cmake_policy(SET CMP0048 NEW)
cmake_policy(SET CMP0054 NEW)
cmake_policy(SET CMP0076 NEW)
#cmake_policy(SET CMP0079 NEW)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if (NOT CMAKE_INSTALL_PREFIX)
   set(CMAKE_INSTALL_PREFIX /usr/local)
endif()

# The project version number.
set(VERSION_MAJOR   0   CACHE STRING "Project major version number.")
set(VERSION_MINOR   1   CACHE STRING "Project minor version number.")
set(VERSION_PATCH   0   CACHE STRING "Project patch version number.")
set(CIRCULARBUFFER_VERSION "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")
mark_as_advanced(CIRCULARBUFFER_VERSION VERSION_MAJOR VERSION_MINOR VERSION_PATCH)

project(CircularBuffer
        VERSION     0.1.0
        DESCRIPTION "A Qt circular buffer"
        LANGUAGES   CXX C
        )

# Make certain the project is set BEFORE find_package
list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

#==== Qt 5 core gui and widgets ==================================
include("CMakeLists.qt5.in")
#==== DOxygen ====================================================
#include("CMakeLists.doxy.in")
#====GoogleTest ==================================================
#include("CMakeLists.gtest.in")
#==== end of standard includes====================================

set(HEADER_FILES
   ${CMAKE_CURRENT_SOURCE_DIR}/include/sm_libraries/CircularBuffer.h
   )
set(HEADER_FILES_P
   ${CMAKE_CURRENT_SOURCE_DIR}/include/sm_libraries/private/circularbuffer.h
   )

add_library(circularbuffer INTERFACE)
target_sources(circularbuffer
   INTERFACE
      $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/sm_libraries/CircularBuffer.h>
      $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/sm_libraries/private/circularbuffer.h>
      $<INSTALL_INTERFACE:include/sm_libraries/CircularBuffer.h>
#   PRIVATE
#      include/sm_libraries/private/circularbuffer.h
#      src/CircularBuffer.cpp
)

#target_include_directories(circularbuffer
#   INTERACE
#      $<INSTALL_INTERFACE:include>
##      $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
#)

#target_link_libraries(circularbuffer
#   INTERFACE
#       Qt5::Core Qt5::Gui
#)

#get_property(includes TARGET circularbuffer PROPERTY "INTERFACE_INCLUDE_DIRECTORIES")
#message("Includes " ${includes})
##############################################
# Installation instructions
include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/circularbuffer)
install(DIRECTORY include/sm_libraries DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
install(DIRECTORY include/sm_libraries/private DESTINATION ${CMAKE_INSTALL_PREFIX}/include)

install(TARGETS circularbuffer
    EXPORT circularbuffer-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

#This is required so that the exported target has the name CircularBuffer and not qyamlcpp
set_target_properties(circularbuffer PROPERTIES EXPORT_NAME CircularBuffer)

#Export the targets to a script
install(EXPORT circularbuffer-targets
    FILE
        circularbufferTargets.cmake
    DESTINATION
        ${INSTALL_CONFIGDIR}
)

#Create a ConfigVersion.cmake file
include(CMakePackageConfigHelpers)

message("Install directory : " ${INSTALL_CONFIGDIR})

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/circularbufferConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(
   ${CMAKE_CURRENT_LIST_DIR}/cmake/circularbufferConfig.cmake.in
   ${CMAKE_CURRENT_BINARY_DIR}/circularbufferConfig.cmake
   INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

#Install the config, configversion and custom find modules
install(FILES
    ${CMAKE_CURRENT_LIST_DIR}/cmake/FindCircularBufferConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/circularbufferConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/circularbufferConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)
