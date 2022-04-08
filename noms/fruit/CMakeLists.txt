set(component fruit)
set(VERSION "1.2.11")

add_library(${component} SHARED src/apple.cpp)

# namespaced alias
add_library(${project}::${component} ALIAS ${component})

target_include_directories(${component}
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

# When COMPONENT is specified at the bottom tyring to install munch results
# in an error in which noms::fruit references nonexistent
# path usr/local/COMPONENT in INTERFACE_INCLUDE_DIRECTORIES (in Ubuntu).
# On windows, it was referencing nonexistent lib/fruit.lib
# Now that I have done this, on Linux at least that seems to be resolved.
# It appears to be important that INCLUDES DESTINATION is the last parameter!
# This does reflect the parameter order in the documentation for the install
# function

# install(TARGETS ${component} EXPORT ${component}-targets
#   COMPONENT ${component}
#   LIBRARY DESTINATION lib
#   ARCHIVE DESTINATION lib
#   RUNTIME DESTINATION bin
#   INCLUDES DESTINATION include
# )

# install(EXPORT ${component}-targets
#   FILE "${project}-${component}-targets.cmake"
#   NAMESPACE ${project}::
#   DESTINATION lib/cmake/${project}
#   COMPONENT ${component}
# )

# # This seems like a kludge, but it does place the file in the correct location
# # on my machine (Ubuntu 18.04). Idea taken from Poco
# configure_file("${component}-config.cmake.in"
#   "${CMAKE_BINARY_DIR}/${project}-${component}-config.cmake"
#   @ONLY
# )

# include(CMakePackageConfigHelpers)
# write_basic_package_version_file(
#   "${CMAKE_BINARY_DIR}/${project}-${component}-config-version.cmake"
#   VERSION ${version}
#   COMPATIBILITY AnyNewerVersion
# )

# install(
#   FILES
#     "${CMAKE_BINARY_DIR}/${project}-${component}-config.cmake"
#     "${CMAKE_BINARY_DIR}/${project}-${component}-config-version.cmake"
#   DESTINATION "${CONFIG_EXPORT_DIR}"
#   COMPONENT ${component}
# )

# install(
#   DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include
#   COMPONENT ${component}
#   DESTINATION ${CMAKE_INSTALL_PREFIX}
# )
include(CMakePackageConfigHelpers)
include(CMakeDependentOption)
include(CheckCXXCompilerFlag)
include(GNUInstallDirs)

include(CheckTypeSize)
include(CheckFunctionExists)
include(CheckIncludeFile)
include(CheckCSourceCompiles)

set_target_properties(${component} PROPERTIES
  VERSION "${PROJECT_VERSION}"
  SOVERSION "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}"
  PROJECT_LABEL "${component} ${${component}-label-postfix}"
  DEBUG_POSTFIX "${CMAKE_DEBUG_POSTFIX}")

# FIXME(felix2012): A more common place for the cmake export would be
# `CMAKE_INSTALL_LIBDIR`, as e.g. done in ubuntu or in this project for GTest
set(CONFIG_EXPORT_DIR "${CMAKE_INSTALL_DATADIR}/cmake/${project}")
set(EXPORT_TARGETS ${component})

configure_package_config_file(
  "cmake/${component}-config.cmake.in"
  "${PROJECT_BINARY_DIR}/${project}-${component}-config.cmake"
  INSTALL_DESTINATION "${CONFIG_EXPORT_DIR}"
  PATH_VARS CMAKE_INSTALL_INCLUDEDIR CONFIG_EXPORT_DIR)
unset(EXPORT_TARGETS)

write_basic_package_version_file(
  "${PROJECT_BINARY_DIR}/${project}-${component}-config-version.cmake"
  VERSION ${VERSION}
  COMPATIBILITY AnyNewerVersion)

install(TARGETS ${component} EXPORT ${component}-targets
    COMPONENT ${component}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    )
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/
    COMPONENT ${component}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    FILES_MATCHING PATTERN "*.h"
    )
install(EXPORT ${component}-targets
    FILE "${project}-${component}-targets.cmake"
    NAMESPACE ${project}::
    DESTINATION "${CONFIG_EXPORT_DIR}"
    COMPONENT ${component}
    )
install(FILES
    "${PROJECT_BINARY_DIR}/${project}-${component}-config.cmake"
    "${PROJECT_BINARY_DIR}/${project}-${component}-config-version.cmake"
    DESTINATION "${CONFIG_EXPORT_DIR}"
    COMPONENT ${component}
    )
# install(FILES "${PROJECT_BINARY_DIR}/zlib.pc"
#     DESTINATION ${CMAKE_INSTALL_DATADIR}/pkgconfig
#     )
