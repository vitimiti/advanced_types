cmake_minimum_required(VERSION 3.2)

option(ADVANCED_TYPES_INSTALL_LIBRARY "Whether to install the library or not"
       OFF)

project(
  ADVANCED_TYPES
  VERSION 0.0.1
  DESCRIPTION "Common pre-processor definitions"
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
