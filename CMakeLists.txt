# ==================================================================================================
# The MIT License (MIT)
#
# Copyright (c) 2023 Victor Matia <vmatir@gmail.com>
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the “Software”), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.
# ==================================================================================================

cmake_minimum_required(VERSION 3.2)

option(ADVANCED_TYPES_INSTALL_LIBRARY "Whether to install the library or not"
       OFF)

project(
  ADVANCED_TYPES
  VERSION 1.0.0
  DESCRIPTION "Common advanced types for safety and ease of use"
  HOMEPAGE_URL "https://github.com/vitimiti/advanced_types"
  LANGUAGES C CXX)

add_subdirectory(vendor)

add_library(${PROJECT_NAME} INTERFACE)
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

target_include_directories(
  ${PROJECT_NAME}
  INTERFACE $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
            $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDE_DIR}>
            ${PREPROC_CONFIG_SOURCE_DIR}/include)

target_precompile_headers(${PROJECT_NAME} INTERFACE <cstdint> <optional>
                          <string_view> <variant>)

include(GNUInstallDirs)
if(PREPROC_INSTALL_LIBRARY)
  message(TRACE
          "Configuring installation system with prefix ${CMAKE_INSTALL_PREFIX}")

  install(
    TARGETS ADVANCED_TYPES
    EXPORT ADVANCED_TYPES_TARGETS
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_LIBDIR})

  include(CMakePackageConfigHelpers)
  write_basic_package_version_file(
    "advanced_types-config-version.cmake"
    VERSION ${ADVANCED_TYPES_VERSION}
    COMPATIBILITY SameMajorVersion)

  configure_package_config_file(
    "${PROJECT_SOURCE_DIR}/cmake/advanced_types-config.cmake.in"
    "${PROJECT_BINARY_DIR}/advanced_types-config.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/advanced_types)

  install(
    EXPORT ADVANCED_TYPES_TARGETS
    FILE advanced_types-targets.cmake
    NAMESPACE ${PROJECT_NAME}::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/advanced_types)

  install(FILES "${PROJECT_BINARY_DIR}/advanced_types-config.cmake"
                "${PROJECT_BINARY_DIR}/advanced_types-config-version.cmake"
          DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/advanced_types)

  install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/advanced_types
          DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
endif()
