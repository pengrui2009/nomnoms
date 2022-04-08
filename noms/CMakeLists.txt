cmake_minimum_required(VERSION 3.0)
set(project noms)
set(version 1.0.0)

project(${project} VERSION 1.2.0 LANGUAGES CXX)

add_subdirectory(fruit)
add_subdirectory(veg)

# UPDATE: implement advice from Tsyvarev

# configure_file(${project}-config.cmake
#   "${CMAKE_BINARY_DIR}/${project}-config.cmake"
#   @ONLY
# )

# include(CMakePackageConfigHelpers)
# write_basic_package_version_file(
#   "${CMAKE_BINARY_DIR}/${project}-config-version.cmake"
#   VERSION ${version}
#   COMPATIBILITY AnyNewerVersion
# )

# install(
#   FILES
#     "${CMAKE_BINARY_DIR}/${project}-config.cmake"
#   DESTINATION lib/cmake/${project}
# )
include(CMakePackageConfigHelpers)
include(CMakeDependentOption)
include(CheckCXXCompilerFlag)
include(GNUInstallDirs)

include(CheckTypeSize)
include(CheckFunctionExists)
include(CheckIncludeFile)
include(CheckCSourceCompiles)

# FIXME(felix2012): A more common place for the cmake export would be
# `CMAKE_INSTALL_LIBDIR`, as e.g. done in ubuntu or in this project for GTest
set(CONFIG_EXPORT_DIR "${CMAKE_INSTALL_DATADIR}/cmake/${project}")

write_basic_package_version_file(
    "${PROJECT_BINARY_DIR}/${project}-config-version.cmake"
    COMPATIBILITY AnyNewerVersion)
  
# install(FILES
#     "${PROJECT_BINARY_DIR}/${project}-config.cmake"
#     "${PROJECT_BINARY_DIR}/${project}-config-version.cmake"
#     DESTINATION "${CONFIG_EXPORT_DIR}"
#     )
# install(FILES "${PROJECT_BINARY_DIR}/zlib.pc"
#     DESTINATION ${CMAKE_INSTALL_DATADIR}/pkgconfig
#     )
install(FILES
    "${PROJECT_SOURCE_DIR}/cmake/${project}-config.cmake"
    DESTINATION "${CONFIG_EXPORT_DIR}"
    )