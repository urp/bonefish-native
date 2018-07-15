cmake_minimum_required( VERSION 3.9 FATAL_ERROR )

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

project(bonefish-native LANGUAGES CXX VERSION 0.1.0 )

### Define library target ###

add_library( ${PROJECT_NAME} INTERFACE )
add_library( ${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME} )

target_include_directories( ${PROJECT_NAME}
  INTERFACE
    $<BUILD_INTERFACE: ${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/${CMAKE_INSTALL_INCLUDEDIR}>
)

### Install and export target set ###

install( TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}-targets
  COMPONENT
    dev
)

install( DIRECTORY include/
  DESTINATION
    ${CMAKE_INSTALL_INCLUDEDIR}
  COMPONENT
    dev
)

### Create package config in build directory ###

set( build_config_location ${CMAKE_CURRENT_BINARY_DIR} )

export( EXPORT ${PROJECT_NAME}-targets
  FILE
    "${build_config_location}/${PROJECT_NAME}-targets.cmake"
  NAMESPACE
    ${PROJECT_NAME}::
)

write_basic_package_version_file(
  "${build_config_location}/${PROJECT_NAME}-config-version.cmake"
  VERSION
    ${PROJECT_VERSION}
  COMPATIBILITY
    AnyNewerVersion
)

configure_package_config_file(
  "package-config.cmake.in"
  "${PROJECT_NAME}-config.cmake"
  INSTALL_DESTINATION
    "${build_config_location}"

)

### Export build directory into local cmake repository ###

export( PACKAGE ${PROJECT_NAME} )

### Install package config ###

set( install_config_location ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME} )

install( EXPORT ${PROJECT_NAME}-targets
  DESTINATION
    ${install_config_location}
  NAMESPACE
    ${PROJECT_NAME}::
  COMPONENT
    dev
)

install(
  FILES
    "${build_config_location}/${PROJECT_NAME}-config.cmake"
    "${build_config_location}/${PROJECT_NAME}-config-version.cmake"
  DESTINATION
    ${install_config_location}
  COMPONENT
    dev
)
